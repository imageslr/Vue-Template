<script>
  import { bitable } from '@lark-base-open/js-sdk';
  import { ref, onMounted } from 'vue';
  import {
    ElButton,
    ElForm,
    ElFormItem,
    ElSelect,
    ElOption,
  } from 'element-plus';

  export default {
    components: {
      ElButton,
      ElForm,
      ElFormItem,
      ElSelect,
      ElOption,
    },
    setup() {
      const formData = ref({ table: '' });
      const tableMetaList = ref([]);
      const doradoId = ref('')
      const confMeta = ref('')

      const addRecord = async () => {
        const tableId = formData.value.table;
        if (tableId) {
          const table = await bitable.base.getTableById(tableId);
          table.addRecord({ fields: {} });
        }
      };

      onMounted(async () => {
        const [tableList, selection] = await Promise.all([bitable.base.getTableMetaList(), bitable.base.getSelection()]);
        formData.value.table = selection.tableId;
        tableMetaList.value = tableList;
      });

      const fetchConfMeta = async () => {
        if (!doradoId.value) {
          console.log("empty dorado id")
          return
        }
        try {
          // 添加请求头
          const headers = new Headers({
            'authorization': 'QjYwMjRDNkNEM0Q2MUYwNkZBMDJBOEZCNEM3RjI0MDE'
          });

          // 使用带有请求头的 fetch 函数
          const response = await fetch('https://openapi-dp.byted.org/openapi/dorado/task/' + doradoId.value, {
            method: 'GET',
            headers: headers
          });

          const data = await response.json();
          confMeta.value = data;

          // 打印结果
          console.log(data);
        } catch (error) {
          console.error('Error fetching conf meta:', error);
        }
      }

      return {
        formData,
        tableMetaList,
        addRecord,
        doradoId,
        confMeta,
        fetchConfMeta,
      };
    },
  };
</script>

<template>
  <el-form ref="form" class="form" :model="formData" label-position="top">
    <el-form-item label="开发指南">
      <a
        href="https://bytedance.feishu.cn/docx/HazFdSHH9ofRGKx8424cwzLlnZc"
        target="_blank"
        rel="noopener noreferrer"
      >
        多维表格插件开发指南
      </a>
      、
      <a
        href="https://lark-technologies.larksuite.com/docx/HvCbdSzXNowzMmxWgXsuB2Ngs7d"
        target="_blank"
        rel="noopener noreferrer"
      >
        Base Extensions Guide
      </a>
    </el-form-item>
    <el-form-item label="API">
      <a
        href="https://bytedance.feishu.cn/docx/HjCEd1sPzoVnxIxF3LrcKnepnUf"
        target="_blank"
        rel="noopener noreferrer"
      >
        多维表格插件API
      </a>
      、
      <a
        href="https://lark-technologies.larksuite.com/docx/Y6IcdywRXoTYSOxKwWvuLK09sFe"
        target="_blank"
        rel="noopener noreferrer"
      >
        Base Extensions Front-end API
      </a>
    </el-form-item>
    <el-form-item label="选择数据表" size="large">
        <el-select v-model="formData.table" placeholder="请选择数据表" style="width: 100%">
          <el-option
            v-for="meta in tableMetaList"
            :key="meta.id"
            :label="meta.name"
            :value="meta.id"
          />
        </el-select>
    </el-form-item>
    <el-form-item label="Dorado ID" size="large">
      <el-input v-model="doradoId" placeholder="请输入 Dorado ID" style="width: 100%"/>
    </el-form-item>
    <el-button type="primary" plain size="large" @click="fetchConfMeta">获取任务的 conf_meta</el-button>
  </el-form>
  <pre :model="confMeta"></pre>
</template>

<style scoped>
  .form :deep(.el-form-item__label) {
    font-size: 16px;
    color: var(--el-text-color-primary);
    margin-bottom: 0;
  }
  .form :deep(.el-form-item__content) {
    font-size: 16px;
  }
</style>
