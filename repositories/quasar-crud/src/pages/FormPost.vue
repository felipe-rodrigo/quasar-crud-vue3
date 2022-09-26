<template>
  <q-page padding>
    <q-form
      @submit="onSubmit"
      class="row q-col-gutter-lg"
    >
    <q-input
        outlined=""
        v-model="form.title"
        label="Título"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
      />
      <q-input
        outlined=""
        v-model="form.author"
        label="Autor"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
      />
      <div class="col-lg-12 col-xs-12">
        <q-editor
        v-model="form.content"
        min-height="5rem"
        :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"/>
      </div>
      <div class="col-12 q-gutter-md">
        <q-btn
        label="Salvar"
        color="secondary"
        class="float-right"
        icon="save"
        type="submit"
        />
        <q-btn
        label="Cancelar"
        color="white"
        class="float-right"
        text-color="secondary"
        :to="{ name: 'home' }"
        />
      </div>
    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'
export default defineComponent({
  name: 'FormPost',
  setup () {
    const { post, getById, update } = postsService()
    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()
    const form = ref({
      title: '',
      content: '',
      author: ''
    })

    onMounted(async () => {
      if (route.params.id) {
        getPost(route.params.id)
      }
    })

    const getPost = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
      } catch (error) {
        console.log(error)
      }
    }

    const onSubmit = async (id) => {
      try {
        if (form.value.id) {
          await update(form.value)
        } else {
          await post(form.value)
        }
        $q.notify({ message: 'Salvo com sucesso', icon: 'check', color: 'positive' })
        router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
    }

    return {
      form,
      onSubmit
    }
  }
})
</script>
