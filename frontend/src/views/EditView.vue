<script setup>
import ParticipationForm from "@/components/ParticipationForm.vue";
</script>

<template>
  <div class="edit">
    <ParticipationForm />
  </div>
</template>

<style scoped>
  @media (min-width: 1024px) {
    .edit {
      min-height: 100vh;
      display: flex;
      align-items: center;
    }
  }
</style>
