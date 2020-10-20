<template>
  <q-page class="items-center justify-evenly">
    <q-card flat bordered class="appeal-card text-center" v-show="isSend">
      <div class="appeal-link">Обращение отправлено!</div>
      <div class="appeal-link">Вы можете отслеживать статус по ссылке: <a :href="link">{{ link }}</a></div>
      <q-btn color="primary" label="Скопировать ссылку" @click="copyLink" />
    </q-card>
    <q-card flat bordered class="appeal-card">
      <q-form
        @submit="onSubmit"
        class="q-gutter-md"
      >
        <div class="row">
          <div class="col q-mr-sm">
            <q-input
              filled
              v-model="from"
              label="Имя"
              hint="Имя и фамилия обратившегося"
            />
          </div>
          <q-space />
          <div class="col">
            <q-input
              filled
              v-model="email"
              label="E-mail"
              hint="Адрес вашей электронной почты"
            />
          </div>
        </div>

        <q-input
          v-model="appeal_text"
          filled
          type="textarea"
          label="Обращение"
          hint="Опишите причину вашего обращения"
        />

        <div>
          <q-btn label="Отправить" type="submit" color="primary" :disable="(from === '' || email === '' || appeal_text === '')" v-show="!isSend" />
        </div>
      </q-form>
    </q-card>
  </q-page>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api'
import { uid, copyToClipboard } from 'quasar'
import { api } from '../api'
import axios from 'axios'
export default defineComponent({
  name: 'Index',
  data () {
    return {
      isSend: false,
      id: '',
      from: '',
      email: '',
      appeal_text: '',
      link: '/#/appeal/87452649'
    }
  },
  methods: {
    onSubmit () {
      this.$q.loading.show()
      this.id = uid()
      axios.post(
        api + 'api/appeal/add', {
          id: this.id,
          from: this.from,
          email: this.email,
          appeal_text: this.appeal_text
        }).then(() => {
        this.isSend = true
        this.link = window.location.origin + '/#/appeal/' + this.id
        this.$q.loading.hide()
      }).catch((error) => {
        console.error('send appeal failed', error)
        this.$q.loading.show()
      })
    },
    copyLink () {
      copyToClipboard(this.link)
        .then(() => {
          this.$q.notify({
            message: 'Ссылка скопирована',
            color: 'primary'
          })
        }).catch((e) => {
          console.error('error while copy to clipboard', e)
        })
    }
  }
})
</script>

<style>
.appeal-card {
  margin: 20px;
  padding: 10px;
}
.appeal-link {
  font-size: 16px;
  font-weight: bold;
  color: #1976D2;
  padding-bottom: 5px;
}
.appeal-link > a {
  color: #1976D2;
  text-decoration: none;
}
</style>
