<template>
  <div>
    <n-config-provider :theme="theme">
      <n-global-style />
      <n-message-provider>
        <NuxtLayout>
          <NuxtPage />
        </NuxtLayout>
      </n-message-provider>
    </n-config-provider>
  </div>
</template>

<script setup>
import { NConfigProvider, NGlobalStyle, NMessageProvider } from "naive-ui";

const theme = null; // Use light theme
</script>
