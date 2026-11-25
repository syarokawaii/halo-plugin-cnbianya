<script setup lang="ts">
// @ts-nocheck
import { ref, watch } from "vue";
import Title from "./Title.vue";
import { Table, Button, Textarea, Divider } from "ant-design-vue";
import { postDetail } from "../postDetail";

const dataSource = ref([]);

const columns = [
  {
    title: "问题 (Q)",
    dataIndex: "q",
    key: "q",
    width: "35%",
  },
  {
    title: "回答 (A)",
    dataIndex: "a",
    key: "a",
  },
  {
    title: "操作",
    key: "action",
    width: 220,
  },
];

function handleAdd() {
  dataSource.value.push({ q: "", a: "" });
}

function handleDel(index) {
  dataSource.value.splice(index, 1);
}

function handleMoveUp(index) {
  if (index > 0) {
    const temp = dataSource.value[index];
    dataSource.value[index] = dataSource.value[index - 1];
    dataSource.value[index - 1] = temp;
  }
}

function handleMoveDown(index) {
  if (index < dataSource.value.length - 1) {
    const temp = dataSource.value[index];
    dataSource.value[index] = dataSource.value[index + 1];
    dataSource.value[index + 1] = temp;
  }
}

defineExpose({ dataSource });

watch(
  () => postDetail.value,
  (val) => {
    const data = val?.metadata?.annotations?.faq;
    const parsed = JSON.parse(data || "[]") || [];
    dataSource.value = parsed.map((item) => ({
      q: item.q ?? item.question ?? "",
      a: item.a ?? item.answer ?? "",
    }));
  }
);
</script>

<template>
  <div>
    <Title title="常见问题 (FAQ)" tip="未配置时默认使用主题通用 FAQ" :tipType="1">
      <Button @click="handleAdd">新增</Button>
    </Title>
    <Table :dataSource="dataSource" :columns="columns" :pagination="false">
      <template #bodyCell="{ column, record, index }">
        <template v-if="column.key === 'q'">
          <Textarea
            allow-clear
            v-model:value="record.q"
            :auto-size="{ minRows: 2, maxRows: 4 }"
            placeholder="请输入问题"
          />
        </template>
        <template v-if="column.key === 'a'">
          <Textarea
            allow-clear
            v-model:value="record.a"
            :auto-size="{ minRows: 2, maxRows: 6 }"
            placeholder="请输入回答（支持 HTML）"
          />
        </template>
        <template v-if="column.key === 'action'">
          <Button size="small" type="primary" danger @click="handleDel(index)"
            >删除</Button
          >

          <Divider type="vertical" />

          <Button
            size="small"
            class="mr-2"
            @click="handleMoveUp(index)"
            :disabled="index === 0"
            >上移</Button
          >
          <Button
            @click="handleMoveDown(index)"
            size="small"
            :disabled="index === dataSource.length - 1"
            >下移</Button
          >
        </template>
      </template>
    </Table>
  </div>
</template>
