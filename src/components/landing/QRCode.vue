<script setup lang="ts">
import { state } from "@/modules/theme"
</script>

<template>
  <Dialog>
    <DialogTrigger
      v-motion-fade
      as-child
      class="fixed right-4 bottom-4 z-50 flex flex-col items-center gap-4"
    >
      <div>
        <span class="text-muted-foreground">Scan to get the app</span>
        <img
          :src="`/assets/images/qr-code-${state}.svg`"
          alt="QR Code"
          class="bg-muted/50 h-28 w-28 cursor-pointer rounded-xl border p-1 opacity-75 backdrop-blur-xl transition hover:scale-110 hover:opacity-100"
        />
      </div>
    </DialogTrigger>
    <DialogContent class="w-sm max-w-fit">
      <DialogHeader class="items-center">
        <DialogTitle>Get the app</DialogTitle>
        <DialogDescription>
          Scan the QR code to download the app
        </DialogDescription>
      </DialogHeader>
      <div class="flex items-center justify-center">
        <img
          :src="`/assets/images/qr-code-${state}.svg`"
          alt="QR Code"
          class="w-full"
        />
      </div>
    </DialogContent>
  </Dialog>
</template>
