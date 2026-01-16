<script setup lang="ts">
import { ref } from 'vue'
import Dropdown from '@/components/Dropdown.vue'
import type { DropdownOption } from '@/types/dropdown'

const selected = ref<DropdownOption | null>(null)

const options: DropdownOption[] = [
  { id: 1, name: 'Option 1', value: 1 },
  { id: 2, name: 'Option 2', value: 2 },
  { id: 3, name: 'Option 3', value: 3 },
  { id: 4, name: 'Option 4', value: 4 },
]
</script>

<template>
  <div class="py-16">
    <h1 class="text-2xl font-bold text-slate-800 mb-6">Dropdown Demo</h1>

    <div class="space-y-16">
      <!-- Options API (ohne TypeScript) -->
      <div>
        <h2 class="text-lg font-semibold text-slate-700 mb-3">Options API</h2>
        <Dropdown
          v-model="selected"
          :values="options"
          button-title="Wähle eine Option"
          placeholder="Bitte auswählen..."
        >
          <template #helper>
            Dies ist ein Hilfetext für das Dropdown (Options API).
          </template>
        </Dropdown>
        <div class="mt-2 p-3 bg-white rounded-lg border border-slate-200">
          <p class="text-sm text-slate-600">
            Ausgewählt:
            <span class="font-medium text-slate-800">
              {{ selected ? selected.name : 'Nichts ausgewählt' }}
            </span>
          </p>
        </div>
      </div>


    </div>
  </div>
</template>
