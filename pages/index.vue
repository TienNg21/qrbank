<script lang="ts" setup>
import type { IBank } from "~/interfaces";
import { bankList } from "~/utils/banks";
const bankOptions = computed(() => {
  return bankList.data.map((bank: any) => {
    return bank;
  });
});
const loading = ref(false);
async function search(q: string) {
  loading.value = true;

  const banks: any[] = bankOptions.value.filter((bank: any) => {
    return (
      bank.name.toLowerCase().includes(q.toLowerCase()) ||
      bank.code.toLowerCase().includes(q.toLowerCase()) ||
      bank.shortName.toLowerCase().includes(q.toLowerCase())
    );
  });

  loading.value = false;

  return banks;
}
const selectedBank = ref<any>(bankOptions.value[0]);
</script>
<template>
  <div>
    <USelectMenu
      :loading="loading"
      v-model="selectedBank"
      :options="bankOptions"
      placeholder="Chọn ngân hàng"
      :searchable="search"
      searchable-placeholder="Tìm kiếm ngân hàng..."
    >
      <template #option="{ option: bank }">
        <img width="50" height="50" :src="bank.logo" alt="logo" />
        <span class="truncate">{{ bank.name }}</span>
      </template>
    </USelectMenu>
  </div>
</template>