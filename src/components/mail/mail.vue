<template>
  <form id="form" method="post">
    <p>
      <label class="form_label">名前 <span class="form_required">必須</span></label>：
      <!-- 入力内容をaddform.nameに格納し、nameValid（エラーメッセージ）があればborderRedクラスをつける -->
      <input v-model="addForm.name" v-bind:class="{ borderRed: nameValid }" class="form_input" required>
    </p>
    <p>
      <!-- addForm.nameで入力内容を表示。入力内容とエラーメッセージの両方があれば：を表示。 -->
      {{ addForm.name }}<span v-if="addForm.name && errorMessage.name">：</span>
      <!-- エラーメッセージの表示 -->
      {{ errorMessage.name }}
    </p>

    <p>
      <label class="form_label">電話番号 <span class="form_required">必須</span></label>：
      <input v-model="addForm.tel" v-bind:class="{ borderRed: telValid }" type="tel" class="form_input" required>
    </p>
    <p>
      {{ addForm.tel }}<span v-if="addForm.tel && errorMessage.tel">：</span>
      {{ errorMessage.tel }}
    </p>

    <p>
      <label class="form_label">メール <span class="form_required">必須</span></label>：
      <input v-model="addForm.mail" v-bind:class="{ borderRed: mailValid }" type="email" class="form_input" required>
    </p>
    <p>
      {{ addForm.mail }}<span v-if="addForm.mail && errorMessage.mail">：</span>
      {{ errorMessage.mail }}
    </p>

    <p>
      <label class="form_label">本文</label>：
      <textarea v-model="addForm.text" class="form_input"></textarea>
    </p>
    <p>{{ addForm.text }}</p>

    <!-- クリックするとsendメソッドが発動。preventでページ遷移しない -->
    <button type="submit" v-on:click.prevent="send">送信</button>
  </form>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'mail',
    data () {
      return {
        addForm: { // フォームに入力された内容
          name: '',
          tel: '',
          mail: '',
          text: ''
        },
        errorMessage: { // 入力が正しくない場合に表示するテキスト
          name: '',
          tel: '',
          mail: ''
        },
        request: { // ajaxで送るデータ
          url: './static/php/form/send.php', // 呼び出すurl
          data: { // phpへ送るデータ
            name: '',
            tel: '',
            mail: '',
            text: '',
            userMsg: 'ユーザー向けメールです。',
            adminMsg: '管理者向けメールです。'
          }
        }
      }
    },

    computed: {
      // 各フォームのバリデーション
      // 入力内容が正しくなければエラーメッセージを挿入
      nameValid: function () {
        if (this.addForm.name) {
          this.errorMessage.name = ''
        } else {
          this.errorMessage.name = '名前を入力してください'
        }

        return this.errorMessage.name
      },
      telValid: function () {
        if (this.addForm.tel.match(/^\d+$/)) {
          this.errorMessage.tel = ''
        } else {
          this.errorMessage.tel = 'ハイフンなしで入力ください'
        }

        return this.errorMessage.tel
      },
      mailValid: function () {
        if (this.addForm.mail.match(/.+@.+\..+/)) {
          this.errorMessage.mail = ''
        } else if (!this.addForm.mail) {
          this.errorMessage.mail = 'メールアドレスを入力ください'
        } else {
          this.errorMessage.mail = 'メールアドレスが正しいかご確認ください'
        }

        return this.errorMessage.mail
      },

      // 各フォームが正しく入力されているか確認
      isValid: function () {
        var valid = false // リセット

        // 入力内容をajaxへ送るデータに格納
        if (!this.errorMessage.name && !this.errorMessage.tel && !this.errorMessage.mail) {
          valid = true
          this.request.data.name = this.addForm.name
          this.request.data.tel = this.addForm.tel
          this.request.data.mail = this.addForm.mail
          this.request.data.text = this.addForm.text
        }

        return valid
      }
    },

    methods: {
      // sendが発動した時の動作
      send: function () {
        var _this = this

        // フォームが正しく入力されている場合
        if (_this.isValid) {
          var requestUrl = _this.request.url
          var requestData = _this.request.data

          var params = new URLSearchParams()
          for (var key in requestData) {
            params.append(key, requestData[key])
          }

          axios
            .post(requestUrl, params)
            // 通信が成功した場合
            .then(function (response) {
              if (response.data.status === 'success') {
                alert('送信しました')
              } else if (response.data.status === 'error') {
                alert('送信に失敗しました。入力を確認してください')
              } else { // statusが返ってこなかった場合
                alert('エラー')
              }
            })
            // 通信に失敗した場合
            .catch(function (e) {
              alert('送信できません。入力を確認してください')
            })
        } else { // フォームの入力が正しくない場合
          alert('入力を確認してください')
        }
      }
    }
  }
</script>

<style>
  .form_label{
    display: inline-block;
    width: 100px;
  }
  .form_input{
    width: 300px;
  }
  .borderRed{
    border-color: red;
  }
  .form_required{
    color: red;
    font-size: 70%;
  }
</style>
