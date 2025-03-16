<script setup>
defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
})
const emit = defineEmits(['onClickRemove'])
</script>

<template>
  <div class="flex items-center border border-slate-200 p-4 rounded-xl gap-4">
    <img :src="imageUrl" alt="Sneakers" class="w-16 h-16" />
    <div class="flex flex-col flex-1">
      <p>{{ title }}</p>

      <div class="flex justify-between mt-2">
        <b class="flex-1">{{ price }} руб.</b>
        <img
          @click="emit('onClickRemove')"
          src="/close.svg"
          alt=""
          class="opacity-40 hover:opacity-100 cursor-pointer transition"
        />
      </div>
    </div>
  </div>
</template>
