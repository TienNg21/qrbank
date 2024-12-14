<script lang="ts" setup>
import type { IBank } from "~/interfaces";
import { bankList } from "~/utils/banks";
import { QRPay, BanksObject } from "vietnam-qr-pay";
import QRCode from "qrcode";

const bankOptions = computed(() => {
  return bankList.data?.map((bank: any) => {
    return bank;
  });
});
const loading = ref(false);
async function search(q = "") {
  loading.value = true;

  const banks: any[] = bankOptions.value?.filter((bank: any) => {
    return (
      bank?.name?.toLowerCase().includes(q.toLowerCase()) ||
      false ||
      bank?.code?.toLowerCase().includes(q.toLowerCase()) ||
      false ||
      bank?.shortName?.toLowerCase().includes(q.toLowerCase()) ||
      false
    );
  });

  loading.value = false;

  return banks;
}
const selectedBank = ref<IBank | null>(bankOptions.value[0]);

const accountNumber = ref("");
const amount = ref("");
const remark = ref("");

const isOpen = ref(false);
const generateQR = async () => {
  console.log(selectedBank.value, accountNumber.value);

  const qrPay = QRPay.initVietQR({
    bankBin: selectedBank.value.bin,
    bankNumber: accountNumber.value,
    amount: amount.value,
    purpose: remark.value,
  });
  const content = qrPay.build();

  qrCodeContent.value = await QRCode.toDataURL(content, { width: 300 });

  isOpen.value = true;
};

const isEnableGenerateButton = computed(() => {
  return selectedBank.value && accountNumber.value;
});

const qrCodeContent = ref("");
</script>
<template>
  <div class="w-50 mx-auto mt-36 flex flex-col">
    <UCard>
      <div class="flex flex-col gap-6 justify-center align-middle">
        <span>Tạo QR</span>
        <USelectMenu
          :loading="loading"
          v-model="selectedBank"
          :modal-value="selectedBank"
          :options="bankOptions"
          placeholder="Chọn ngân hàng"
          option-attribute="name"
          :searchable="search"
          searchable-placeholder="Tìm kiếm ngân hàng..."
        >
          <template #leading>
            <img
              v-if="selectedBank?.logo"
              width="50"
              height="50"
              :src="selectedBank?.logo"
              alt="logo"
            />
          </template>
          <template #label>
            <span v-if="selectedBank?.name" class="truncate ml-10">{{
              selectedBank?.name
            }}</span>
          </template>
          <template #option="{ option: bank }">
            <img width="50" height="50" :src="bank.logo" alt="logo" />
            <span class="truncate">{{ bank.name }}</span>
          </template>
        </USelectMenu>

        <UInput
          v-model="accountNumber"
          :required="true"
          placeholder="Nhập số tài khoản"
        />
        <UInput v-model="amount" placeholder="Nhập số tiền" />
        <UInput v-model="remark" placeholder="Nhập nội dung" />
        <UButton @click="generateQR" :disabled="!isEnableGenerateButton"
          >Generate</UButton
        >
      </div>
    </UCard>

    <UModal v-model="isOpen">
      <UCard>
        <template #header>
          <div class="flex items-center justify-between">
            <h3
              class="text-base font-semibold leading-6 text-gray-900 dark:text-white"
            >
              QR Code của bạn
            </h3>
          </div>
        </template>
        <div class="p-6 flex flex-col">
          <img :src="qrCodeContent" alt="Qr code" />
        </div>
      </UCard>
    </UModal>
  </div>
</template>