<template>
  <q-page padding>
    <q-table
      title="Treats"
      :rows="posts"
      :columns="columns"
      row-key="name"
      dark
      color="amber"
    >
    <template v-slot:top>
      <span class="text-h4">Artigos</span>
      <q-space/>
      <q-btn color="secondary" label="Novo" :to="{ name: 'formPost'}"/>
    </template>
    <template v-slot:body-cell-actions="props">
      <q-td :props="props">
        <q-btn class="q-ma-sm" icon="edit" color="pink-7" dense size="md" @click="handleEditPost(props.row.id)"/>
        <q-btn class="q-ma-auto" icon="delete" color="negative" dense size="md" @click="handleDeletePost(props.row.id)"/>
      </q-td>
    </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
    const columns = [
      { name: 'id', field: 'id', label: 'Id', sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'Título', sortable: true, align: 'left' },
      { name: 'author', field: 'author', label: 'Autor', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'Ações', sortable: true, align: 'right' }
    ]
    const $q = useQuasar()
    const router = useRouter()

    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          dark: true,
          title: 'Deletar',
          message: 'Certeza que deseja remover?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'Deletado com sucesso', icon: 'check', color: 'positive' })
          await getPosts()
        })
      } catch (error) {
        $q.notify({ message: 'Erro ao Apagar', icon: 'times', color: 'negative' })
      }
    }

    const handleEditPost = (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>
