<template>
  <q-page class="items-center justify-evenly">
    <q-card flat bordered class="appeal-card">
      <q-form
        class="q-gutter-md"
      >
        <div class="row">
          <div class="col q-mr-sm">
            <q-input
              filled
              v-model="from"
              label="Имя"
              hint="Имя и фамилия обратившегося"
              readonly
            />
          </div>
          <q-space />
          <div class="col">
            <q-input
              filled
              v-model="email"
              label="E-mail"
              hint="Адрес вашей электронной почты"
              readonly
            />
          </div>
        </div>

        <q-input
          v-model="appeal_text"
          filled
          type="textarea"
          label="Обращение"
          hint="Опишите причину вашего обращения"
          readonly
        />

        <div>
          <q-badge outline color="red" label="Не прочитано" v-show="!is_read" />
          <q-badge outline color="primary" label="В обработке" v-show="is_read && (status == null || status === '' || status === undefined)" />
        </div>

        <q-badge outline color="positive" label="Обращение принято" v-show="status != null && status !== '' && status === 'processed'" />

        <div v-show="status != null && status !== '' && status === 'rejected'">
          <q-badge outline color="red" label="Обращение отклонено" class="q-mb-md" />

          <q-input
            v-model="reject_reason"
            filled
            type="textarea"
            label="Причина отклонения"
            hint="Причина отклонения обращения"
            readonly
          />
        </div>

      </q-form>
    </q-card>
  </q-page>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api'
import { api } from '../api'
import axios from 'axios'
export default defineComponent({
  name: 'Appeal',
  data () {
    return {
      from: '',
      email: '',
      appeal_text: '',
      is_read: true,
      status: '',
      reject_reason: ''
    }
  },
  mounted () {
    this.$q.loading.show()
    let id = null
    try {
      id = this.$route.params.id
    } catch (e) {
      console.error('trying to get route param (id) failed', e)
      return
    }
    axios.get(`${api}api/appeal/${id}`)
      .then(response => {
        const data = response.data
        this.from = data.from
        this.email = data.email
        this.appeal_text = data.appeal_text
        this.is_read = data.is_read
        this.status = data.status
        this.reject_reason = data.reject_reason
        this.$q.loading.hide()
      }).catch((error) => {
        console.error('error getting appeal', error)
        this.$q.loading.hide()
      })
  }
})
</script>

<style>
.appeal-card {
  margin: 20px;
  padding: 10px;
}
</style>
