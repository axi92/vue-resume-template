<template>
  <form id="contact-form">
    <div class="row contact-form-row align-items-stretch">
      <!-- Feedback Alert -->
      <div class="col-12 mb-1" v-if="alertStatus">
        <Alert :type="alertStatus.type" :message="alertStatus.message" />
      </div>

      <!-- Left Column -->
      <div class="col-xl-6">
        <!-- Name Input -->
        <div class="form-group input-group">
          <span class="input-group-text input-group-attach"><i class="fa fa-signature" /></span>
          <input class="form-control" id="form-name" type="text" v-model="name"
            :placeholder="data.getString('name') + ' *'" required />
        </div>

        <!-- E-mail Address Input -->
        <div class="form-group input-group">
          <span class="input-group-text input-group-attach"><i class="fa fa-envelope" /></span>
          <input class="form-control" id="form-email" type="email" v-model="email"
            :placeholder="data.getString('email') + ' *'" required />
        </div>

        <!-- Subject Input -->
        <!-- <div class="form-group input-group">
          <span class="input-group-text input-group-attach"><i class="fa fa-pen-to-square" /></span>
          <input class="form-control" id="form-subject" type="text" v-model="subject"
            :placeholder="data.getString('subject') + ' *'" required />
        </div> -->
      </div>

      <!-- Right Column -->
      <div class="col-xl-6">
        <!-- Message TextArea -->
        <div class="form-group form-group-textarea mb-md-0">
          <textarea class="form-control" id="form-message" v-model="message" :placeholder="data.getString('message')"
            required />
        </div>
      </div>

      <!-- Bottom Column -->
      <div class="col-12 text-center mt-3 mt-lg-4 d-flex justify-content-center align-items-center gap-3 flex-wrap">
        <button class="btn btn-primary btn-xl" type="submit" id="btn-submit-message"
          :class="{ disabled: submitStatus === SubmitStatus.SENDING }">
          <i class="fa fa-envelope me-1" /> {{ data.getString('sendMessage') }}
        </button>
        <Captcha></Captcha>
      </div>
    </div>
  </form>
</template>

<script setup lang="ts">
import { useData } from "../../../composables/data.js"
import { useLayout } from "../../../composables/layout.js"
import { computed, onMounted, ref } from "vue"
import { useNavigation } from "../../../composables/navigation.js"
import Alert from "../../widgets/Alert.vue"
import Captcha from "../../widgets/Captcha.vue"

const data = useData()
const layout = useLayout()
const navigation = useNavigation()
const WEB3FORMS_ACCESS_KEY = "321d1380-d09b-468c-a898-a0ab1bdef7a8";
const name = ref("")
const email = ref("")
const message = ref("")

/**
 * @enum
 */
const SubmitStatus = {
  ENABLED: "enabled",
  SENDING: "sending",
  SENT: "sent",
  ERROR: "error"
}

/**
 * @const
 * @type {Ref[]}
 */
const FORM_FIELDS: Ref[] = [name, email, message]

/**
 * @type {ref.<SubmitStatus>}
 */
const submitStatus: ref<SubmitStatus> = ref(null)

/**
 * @private
 */
onMounted(() => {
  const form = document.getElementById('contact-form')
  if (form.attachEvent) {
    form.attachEvent("submit", _onSubmit)
  } else {
    form.addEventListener("submit", _onSubmit)
  }

  submitStatus.value = SubmitStatus.ENABLED
})

/**
 * @private
 */
const _clearAllFields = () => {
  FORM_FIELDS.forEach(field => {
    field.value = '';
    // const elField = document.getElementById(`form-${field}`)
    // console.log('clear field:', elField);
    // elField.value = ''
  })
}

/**
 * @param e
 * @return {boolean}
 * @private
 */
const _onSubmit = (e): boolean => {
  if (e.preventDefault) {
    e.preventDefault()
  }

  const values = {}

  // FORM_FIELDS.forEach(field => {
  //   const elField = document.getElementById(`form-${field}`)
  //   values[field] = elField.value
  // })

  submitStatus.value = SubmitStatus.SENDING

  _sendMessage()
  return false
}

interface mailResponse {
  data: {
    email: string
    message: string
    name: string
  }
  message: "string"
  success: boolean
}

async function _submitForm(): Promise<mailResponse> {
  return new Promise(async (resolve, reject) => {
    console.log('in submit form');
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        name: name.value,
        email: email.value,
        message: message.value,
      }),
    });
    const result = await response.json();
    if (result.success) {
      _onMessageSent();
      resolve(result as mailResponse);
    } else {
      _onMessageError()
      reject(result as mailResponse);
    }
  })
}

/**
 * @private
 */
const _sendMessage = async () => {
  const feedbackView = layout.getFeedbackView()
  feedbackView.showActivitySpinner(data.getString("sendingMessage") + "...")

  // _onMessageError()
  // _clearAllFields()
  await _submitForm();
}


/**
 * @private
 */
const _onMessageSent = () => {
  const feedbackView = layout.getFeedbackView()
  feedbackView.hideActivitySpinner()

  _clearAllFields()
  submitStatus.value = SubmitStatus.SENT
  if (navigation.isOneAtOnceMode()) {
    layout.instantScrollTo(0, false)
  }
}

/**
 * @private
 */
const _onMessageError = () => {
  const feedbackView = layout.getFeedbackView()
  feedbackView.hideActivitySpinner()
  submitStatus.value = SubmitStatus.ERROR
}

/**
 * @return {{type: string, message: String}|null}
 * @private
 */
const alertStatus = computed(() => {
  switch (submitStatus.value) {
    case SubmitStatus.SENT:
      return { type: 'success', message: data.getString('messageSent') }
    case SubmitStatus.ERROR:
      return { type: 'danger', message: data.getString('messageError') }
    default:
      return null
  }
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

$form-input-background-color: lighten($light-3, 20%);
$form-input-placeholder-color: $light-5;

$form-input-border-color: $light-2;
$form-input-border-color-focus: lighten($primary, 5%);

$form-input-group-background-color: $light-1;
$form-input-group-font-color: $headings-color;

input,
textarea {
  padding: 1rem;
  font-family: $headings-font-family;

  background-color: $form-input-background-color;
  color: $dark;
  border: 2px solid $form-input-background-color;
  border-radius: 0;

  &:focus {
    border: 2px solid $form-input-border-color-focus;
  }
}

.input-group {
  @include generate-dynamic-styles-with-hash((xxxl: (margin-bottom: 0.8rem),
      sm: (margin-bottom: 0.4rem)));

  border: 2px solid $form-input-border-color;
}

.input-group-text {
  border: none;
  border-right: 2px solid $form-input-border-color;
  min-width: 60px;
  background-color: $form-input-group-background-color;

  border-radius: 0;
  text-align: center;

  i {
    color: $form-input-group-font-color;
    margin: 0 auto;
  }
}

input {
  @include generate-dynamic-styles-with-hash((xxxl: (height: auto),
      xl: (height: max(50px, 6.5vh)),
      lg: (height: max(40px, 4.5vh)),
      sm: (height: 40px, font-size: 0.8rem),
    ))
}

textarea {
  @include generate-dynamic-styles-with-hash((xxxl: (height: 218px),
      lg: (height: 200px),
      sm: (height: 150px, font-size: 0.8rem)));

  border: 2px solid $form-input-border-color;
}

::-webkit-input-placeholder {
  @include generate-dynamic-styles-with-hash((xxxl: (font-size: 1.1rem),
      lg: (font-size: 0.9rem)));

  font-family: $headings-font-family;
  color: $light-5;
}

.col-12.text-center.mt-3.mt-lg-4 {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem; // Adjust the spacing as needed
  flex-wrap: wrap; // Allow wrapping on smaller screens
}

@media (max-width: 576px) {

  // Breakpoint for smaller screens
  .col-12.text-center.mt-3.mt-lg-4 {
    flex-direction: column; // Stack items vertically
    gap: 0.5rem; // Reduce spacing for smaller screens
  }
}
</style>