<template>

  <a-button type="danger">Danger</a-button>
  <div class="home">
    <a-button type="danger">
      Danger
    </a-button>
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
  </div>
</template>

<script lang="ts">
    import {defineComponent, onMounted, ref, reactive, toRef} from 'vue';
    import axios from 'axios';
    import {message} from "ant-design-vue";
    import {Tool} from "@/util/tool";
    import TheWelcome from '@/components/the-welcome.vue'


    // const listData: any = [];
    //
    // for (let i = 0; i < 23; i++) {
    //     listData.push({
    //         href: 'https://www.antdv.com/',
    //         title: `ant design vue part ${i}`,
    //         avatar: 'https://zos.alipayobjects.com/rmsportal/ODTLcjxAfvqbxHnVXCYX.png',
    //         description:
    //             'Ant Design, a design language for background applications, is refined by Ant UED Team.',
    //         content:
    //             'We supply a series of design principles, practical patterns and high quality design resources (Sketch and Axure), to help people create their product prototypes beautifully and efficiently.',
    //     });
    // }

    export default defineComponent({
        name: 'Home',
        components: {
          TheWelcome
        },
        setup() {
            const ebooks = ref();

            const level1 = ref();
            let categorys: any;

            /**
             * 查询所有分类
             */
            const handleQueryCategory = () => {
                axios.get("/category/all").then((response) => {
                    const data = response.data;
                    if (data.success) {
                        categorys = data.content;
                        console.log("原始数据：", categorys);

                        level1.value = [];
                        level1.value = Tool.array2Tree(categorys, 0);
                        console.log("树形结构：", level1.value);
                    } else {
                        message.error(data.message);
                    }
                });
            };

            const isShowWelcome = ref(true);
            let categoryId2 = 0;

            const handleQueryEbook = () => {
                axios.get("/ebook/list", {
                    params: {
                        page: 1,
                        size: 1000,
                        categoryId2: categoryId2
                    }
                }).then((response) => {
                    const data = response.data;
                    ebooks.value = data.content.list;
                    //ebook1.books =data.content;
                });
            };

            const handleClick = (value: any) => {
                console.log("menu click", value);
                // isShowWelcome.value = value.key === 'welcome';
                if (value.key === 'welcome') {
                    isShowWelcome.value = true;
                } else {
                    categoryId2 = value.key;
                    isShowWelcome.value = false;
                    handleQueryEbook();
                }
            };

            onMounted(() => {
                // function (response) {}相当于(response)=> {}

                handleQueryCategory();
                // handleQueryEbook()

            });
            return {
                ebooks,
                //ebook2:toRef(ebook1,"books"),
                //listData,
                pagination: {
                    onChange: (page: any) => {
                        console.log(page);
                    },
                    pageSize: 3,
                },
                handleClick,
                level1,

                isShowWelcome,
            }
        }
    });
</script>

<!--只在当前页面生效-->
<style scoped>
    .ant-avatar {
        width: 50px;
        height: 50px;
        line-height: 50px;
        border-radius: 8%;
        margin: 5px 0;
    }
</style>