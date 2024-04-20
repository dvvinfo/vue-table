<template>
  <div class="container">
    <template v-if="dataSource.length > 0">
      <a-table :columns="columns" :data-source="dataSource" row-key="id" :pagination="pagination">
        <template #expandedRowRender="{ record }">
          <p>{{ record.description }}</p>
        </template>
      </a-table>
    </template>
    <a-flex  justify="center" v-else >
    <div class="ant-empty css-dev-only-do-not-override-1hsjdkk ant-empty-normal"><div class="ant-empty-image"><svg width="64" height="41" viewBox="0 0 64 41" xmlns="http://www.w3.org/2000/svg"><g transform="translate(0 1)" fill="none" fill-rule="evenodd"><ellipse fill="#f5f5f5" cx="32" cy="33" rx="32" ry="7"></ellipse><g fill-rule="nonzero" stroke="#d9d9d9"><path d="M55 12.76L44.854 1.258C44.367.474 43.656 0 42.907 0H21.093c-.749 0-1.46.474-1.947 1.257L9 12.761V22h46v-9.24z"></path><path d="M41.613 15.931c0-1.605.994-2.93 2.227-2.931H55v18.137C55 33.26 53.68 35 52.05 35h-40.1C10.32 35 9 33.259 9 31.137V13h11.16c1.233 0 2.227 1.323 2.227 2.928v.022c0 1.605 1.005 2.901 2.237 2.901h14.752c1.232 0 2.237-1.308 2.237-2.913v-.007z" fill="#fafafa"></path></g></g></svg></div><h3 class="ant-empty-description">No data</h3><!----></div>
  </a-flex>

    <a-modal v-model:open="modalVisible" title="Добавить запись" @ok="handleOk">
      <a-form :form="form">
        <a-form-item label="Имя" :rules="[{ required: true, message: 'Введите имя' }]">
          <a-input v-model:value="formData.name" />
        </a-form-item>
        <a-form-item label="Описание">
          <a-input v-model:value="formData.description" />
        </a-form-item>
      </a-form>
    </a-modal>

    <a-flex gap="small" justify="center" class="btns">
    <a-button type="primary" @click="showModal">Добавить</a-button>
    <a-button @click="fetchData">Заполнить</a-button>
  </a-flex>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive} from 'vue'
import { message } from 'ant-design-vue'
import axios from 'axios'

interface IRecord {
  id: number;
  name: string;
  description: string;
}

interface IFormData {
  name: string
  description: string
}

const pagination = ref({ pageSize: 5 })
const columns = ref([
  { title: 'Имя', dataIndex: 'name' },
  { title: 'Описание', dataIndex: 'description' }
])

const dataSource = ref<IRecord[]>([])
const modalVisible = ref(false)

const form = reactive<IFormData>({ name: '', description: '' })
const formData = reactive<IFormData>({ ...form })

const fetchData = async () => {
  try {
    const response = await axios.get<IRecord[]>('http://localhost:3000/data')
    dataSource.value = response.data
  } catch (error) {
    console.error(error)
    // message.error('Ошибка при загрузке данных')
  }
}

const showModal = () => {
  modalVisible.value = true
}

const handleOk = async () => {
  if (!formData.name || !formData.description) {
    message.error('Пожалуйста, заполните все обязательные поля')
    return
  }

  try {
    const response = await axios.post('http://localhost:3000/data', formData)
    message.success('Запись успешно добавлена')
    modalVisible.value = false

    // Добавляем только что добавленную запись в dataSource
    const newRecord: IRecord = { id: response.data.id, name: formData.name, description: formData.description }
    dataSource.value.push(newRecord)

    // Очищаем данные формы
    formData.name = ''
    formData.description = ''
  } catch (error) {
    console.error(error)
    message.error('Ошибка при добавлении записи')
  }
}
</script>

<style scoped>
.container{
padding: 20px;
}
.btns{
  margin-top: 20px;
}
</style>
