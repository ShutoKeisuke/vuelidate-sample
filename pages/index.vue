<template>
  <div class="main-container">
    <div class="title-container">
      <h1 class="title-text">How to use Vuelidate</h1>
    </div>
    <div class="form-container">
      <div class="sample-form">
        <div class="sample-form-contents">
          <!--
            バリデーションチェックを行うデータをv-modelで結び付ける
            validationにバリデーションチェックを行うデータとチェックを行う項目を記載すると
            項目に該当する「$v.formData.name.required」がバリデーション対象の真偽値となる
            すべてのチェック項目をクリアすると「$v.formData.name.$invalid」が「false」となる

            $dirtyプロパティ
            $v.formData.name.$touch()メソッドによってfalse → trueへと書き換えられる
            inputイベントにこのメソッドを挿入することで入力済みか否か判断できる

            $errorプロパティ
            $invalidプロパティと$dirtyプロパティの論理積をとる
            これにより、初回表示時は$invalid = true, $dirty = falseとなるため
            $errorプロパティはfalseとなりエラーメッセージを表示しないようなことが実現できる
           -->
          <label class="form-label" for="name">Name</label>
          <input class="form-input" type="name" id="name" v-model="formData.name" @input="$v.formData.name.$touch()">
          <span class="error-msg" :class="{ error : $v.formData.email.$error }" v-if="$v.formData.name.$error">name is required</span>
        </div>
        <div class="sample-form-contents">
          <label class="form-label" for="age">Age</label>
          <input class="form-input" type="age" id="age" v-model="formData.age">
        </div>
        <div class="sample-form-contents">
          <!-- 
            $v.formData.email.$touch()をblurイベントで行うことで入力を終えてblurを行ったときに
            $dirtyがfalse → true となり、初めてバリデーションチェックが行われる
           -->
          <label class="form-label" for="email">Email</label>
          <input class="form-input" type="email" id="email" v-model="formData.email" @blur="$v.formData.email.$touch()">
          <span class="error-msg" :class="{ error : $v.formData.email.$error }" v-if="$v.formData.email.$error && !$v.formData.email.required">email is required</span>
          <span class="error-msg" :class="{ error : $v.formData.email.$error }" v-if="$v.formData.email.$error && !$v.formData.email.email">this is not in email format</span>
        </div>
        <div class="sample-form-submit-contents">
          <!-- <button class="form-button" :disabled="$v.formData.$invalid" type="submit" @click="submitForm">submit</button> -->
          <button class="form-button" type="submit" @click="submitForm">submit</button>
        </div>
      </div>
    </div>
    {{ formData }}
    {{ $v.formData }}
    {{ $v.formData.name.$invalid }}
    {{ $v.formData.name.required }}
  </div>
</template>

<script>
import { required, requiredIf, email } from 'vuelidate/lib/validators'

export default {
  data() {
    return {
      formData: {
        name: null,
        age: null,
        email: null
      }
    }
  },
  validations: {
    formData: {
      name: { required },
      email: { required, email }
    }
  },
  methods: {
    submitForm() {
      // ボタン押下時に$touch()メソッドを呼ぶことで全ての$dirtyプロパティをtrueにしバリデーションチェックを行う状態にする
      this.$v.formData.$touch()
      console.log('----- click submit -----')
      console.log('----- form data -----')
      console.log(`name : ${this.formData.name}`)
      console.log(`name : ${this.formData.age}`)
      console.log(`name : ${this.formData.email}`)
    }
  }
}
</script>

<style lang="scss" scoped>
.main-container {
  & .title-container {
    & .title-text{
      margin: 0;
      padding: 20px 0;
      padding-left: 20px;
    }
  }

  & .form-container {
    width: 70%;
    margin: 0 auto;
    background-color: rgb(255, 255, 255);

    & .sample-form {
      display: flex;
      flex-direction: column;

      & .sample-form-contents {
        display: flex;
        flex-direction: column;
        margin-left: 5%;
        margin-right: 5%;
        margin-top: 20px;

        & .form-label {
          margin-bottom: 2px;
        }

        & .error-msg {
          padding-top: 2px;
          padding-left: 5px;
          
        }
      }

      & .sample-form-submit-contents {
        margin-left: 85%;
        margin-top: 20px;
        margin-bottom: 20px;

        & .form-button{
          background-color: rgb(183, 213, 248);
        }
      }
    }
  }
}
.error {
  color: red;
}
</style>
