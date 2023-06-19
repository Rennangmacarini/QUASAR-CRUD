<template>
<q-page padding>
    <q-table title="Artigos" :rows="posts" :columns="columns" row-key="name">
        <template v-slot:top>
            <span class="text-h5">Artigos</span>
            <q-space />
            <q-btn color="primary" label="Novo" :to="{ name: 'formPost' }" />
        </template>
        <template v-slot:body-cell-actions="props">
            <q-td :props="props" class="q-gutter-sm">
                <q-btn icon="edit" color="info" dense size="sm" @click="handleEditPost(props.row.id)" />
                <q-btn icon="delete" color="negative" dense size="sm" @click="handleDeletePost(props.row.id)" />
            </q-td>
        </template>
    </q-table>
</q-page>
</template>

<script>
import {
    defineComponent,
    ref,
    onMounted
} from "vue";
import postsService from "src/services/post";
import {
    useQuasar
} from "quasar";
import {
    useRouter
} from 'vue-router'

export default defineComponent({
    name: "IndexPage",
    setup() {
        const posts = ref([]);
        const {
            list,
            remove
        } = postsService();
        const columns = [{
                name: "id",
                field: "id",
                label: "Id",
                sortable: true,
                align: "left"
            },
            {
                name: "title",
                field: "title",
                label: "Title",
                sortable: true,
                align: "left",
            },
            {
                name: "author",
                field: "author",
                label: "Author",
                sortable: true,
                align: "left",
            },
            {
                name: "actions",
                field: "actions",
                label: "Actions",
                align: "right"
            },
        ];

        const $q = useQuasar();
        const router = useRouter()

        onMounted(() => {
            getPost();
        });

        const getPost = async () => {
            try {
                const data = await list();
                posts.value = data;
            } catch (error) {
                console.error(error);
            }
        };

        const handleDeletePost = async (id) => {
            try {
                $q.dialog({
                    title: "Remover",
                    message: "Deseja remover este post ?",
                    cancel: true,
                    persistent: true,
                }).onOk(async () => {
                    await remove(id);
                    $q.notify({
                        message: "Removido com sucesso!",
                        icon: "check",
                        color: "positive",
                    });
                    await getPost();
                });
            } catch (error) {
                $q.notify({
                    message: "Erro ao apagar o post",
                    icon: "times",
                    color: "negative",
                });
            }
        };

        const handleEditPost = (id) => {
            router.push({
                name: 'formPost',
                params: {
                    id
                }
            })
        }

        return {
            posts,
            columns,
            handleDeletePost,
            handleEditPost
        };
    },
});
</script>
