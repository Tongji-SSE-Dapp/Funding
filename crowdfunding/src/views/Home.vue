<template>
  <div class="fancy-container">
    <a-card class="ant-card-shadow fancy-card">
      <template #title>
        <div class="fancy-title-container">
          <h3 class="fancy-title">
            全部众筹信息
          </h3>
          <div class="fancy-title-decoration"></div>
          <a-button style="float: right; margin-top:-2em;" @click="openModal" type="primary">发起众筹</a-button>
        </div>
      </template>
      <a-table class="fancy-table" :columns="columns" :loading="state.loading" :data-source="state.data" bordered>
        <template #time="{text, record}">
          <span class="fancy-time">{{new Date(text * 1000).toLocaleString()}}</span>
        </template>
        <template #tag="{text, record}">
          <a-tag class="fancy-tag" color="success" v-if="record.success === true">
            <template #icon>
              <check-circle-outlined class="fancy-icon"/>
            </template>
            众筹成功
          </a-tag>
          <a-tag class="fancy-tag" color="processing" v-else-if="new Date(record.endTime * 1000) > new Date()" >
            <template #icon>
              <sync-outlined :spin="true" class="fancy-icon" />
            </template>
            正在众筹
          </a-tag>
          <a-tag class="fancy-tag" color="error" v-else>
            <template #icon>
              <close-circle-outlined class="fancy-icon" />
            </template>
            众筹失败
          </a-tag>
        </template>
        <template #action="{text, record}">
          <a @click="clickFunding(record.index)" class="fancy-link">查看详情</a>
        </template>
      </a-table>
    </a-card>

    <Modal v-model:visible="isOpen">
      <a-card class="fancy-card" style="width: 600px; margin: 0 2em;" :body-style="{ overflowY: 'auto', maxHeight: '600px' }">
        <template #title>
          <div class="fancy-title-container" style="text-align: center; justify-content: center;">
            <h3 class="fancy-title" style="margin:0;">发起众筹</h3>
            <div class="fancy-title-decoration" style="margin:0 auto; width:50%;"></div>
          </div>
        </template>
        <create-form :model="model" :form="form" :fields="fields" />
      </a-card>
    </Modal>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, reactive } from 'vue';
import Modal from '../components/base/modal.vue'
import CreateForm from '../components/base/createForm.vue'
import { Model, Fields, Form } from '../type/form'
import { contract, getAccount, getAllFundings, Funding, newFunding, addListener } from '../api/contract'
import { message } from 'ant-design-vue'
import { CheckCircleOutlined, SyncOutlined, CloseCircleOutlined } from '@ant-design/icons-vue'
import { useRouter } from 'vue-router'

const columns = [
  {
    dataIndex: 'title',
    key: 'title',
    title: '众筹标题'
  },
  {
    title: '目标金额(eth)',
    dataIndex: 'goal',
    key: 'goal'
  },
  {
    title: '目前金额(eth)',
    dataIndex: 'amount',
    key: 'amount'
  },
  {
    title: '结束时间',
    dataIndex: 'endTime',
    key: 'endTime',
    slots: { customRender: 'time' }
  },
  {
    title: '当前状态',
    dataIndex: 'success',
    key: 'success',
    slots: { customRender: 'tag' }
  },
  {
    title: '详情',
    dataIndex: 'action',
    key: 'action',
    slots: { customRender: 'action' }
  }
]

export default defineComponent({
  name: 'Home',
  components: { Modal, CreateForm, CheckCircleOutlined, SyncOutlined, CloseCircleOutlined },
  setup() {
    const isOpen = ref<boolean>(false);
    const state = reactive<{loading: boolean, data: Funding[]}>({
      loading: true,
      data: []
    })

    async function fetchData() {
      state.loading = true;
      try {
        state.data = await getAllFundings();
        state.loading = false;
      } catch (e) {
        console.log(e);
        message.error('获取众筹失败!');
      }
    }

    async function openModal() {
      model.account = await getAccount();
      isOpen.value = true;
    }
    function closeModal() { isOpen.value = false; }

    const model = reactive<Model>({
      account: '',
      title: '',
      info: '',
      amount: 0,
      date: null,
    })

    const fields = reactive<Fields>({
      account: {
        type: 'input',
        label: '发起人',
        disabled: true
      },
      title: {
        type: 'input',
        label: '标题',
        rule: {
          required: true,
          trigger: 'blur'
        }
      },
      info: {
        type: 'textarea',
        label: '简介',
        rule: {
          required: true,
          trigger: 'blur'
        }
      },
      amount: {
        type: 'number',
        label: '金额',
        min: 0
      },
      date: {
        type: 'time',
        label: '截止日期',
      }
    })

    const form = reactive<Form>({
      submitHint: '发起众筹',
      cancelHint: '取消',
      cancel: () => {
        closeModal();
      },
      finish: async () => {
        const seconds = Math.ceil(new Date(model.date).getTime() / 1000);
        try {
          const res = await newFunding(model.account, model.title, model.info, model.amount, seconds);
          console.log(res)
          message.success('发起众筹成功')
          closeModal();
          fetchData();
        } catch(e) {
          console.log(e);
          message.error('发起众筹失败')
        }
      }
    })

    const router = useRouter();
    const clickFunding = (index : number) => {
      router.push(`/funding/${index}`)
    }
    addListener(fetchData)
    fetchData();

    return { openModal, isOpen, model, fields, form, state, columns, clickFunding }
  }
});
</script>

<style scoped>
.fancy-container {
  padding: 1em;
  background: linear-gradient(to bottom right, #f0f5ff, #e6f7ff);
  transition: all 0.5s ease;
}

.fancy-card {
  background: linear-gradient(to bottom right, #ffffff 0%, #f6f9ff 100%);
  border-radius: 10px;
  overflow: hidden;
  transition: all 0.3s ease-in-out;
  margin-bottom: 1em;
}
.fancy-card:hover {
  box-shadow: 0 0 20px rgba(0,0,0,0.1);
}

.fancy-title-container {
  display: flex;
  align-items: center;
  position: relative;
}

.fancy-title {
  font-size: 1.5em;
  font-weight: bold;
  background: linear-gradient(45deg, #1890ff, #52c41a);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin: 0;
}

.fancy-title-decoration {
  flex: 1;
  height: 2px;
  background: linear-gradient(to right, #1890ff, #52c41a);
  margin-left: 1em;
  border-radius: 2px;
}

.fancy-table {
  background: transparent;
}

.fancy-time {
  font-weight: bold;
  color: #555;
  transition: color 0.3s;
}
.fancy-time:hover {
  color: #1890ff;
}

.fancy-link {
  color: #1890ff;
  text-decoration: none;
  font-weight: bold;
  transition: color 0.3s;
}
.fancy-link:hover {
  color: #52c41a;
}

.fancy-tag {
  font-weight: bold;
  animation: glow 2s infinite ease-in-out alternate;
}
.fancy-icon {
  margin-right: 0.3em;
}

@keyframes glow {
  0% {
    box-shadow: 0 0 5px rgba(24,144,255,0.2);
  }
  100% {
    box-shadow: 0 0 15px rgba(24,144,255,0.8);
  }
}
</style>
