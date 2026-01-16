<template>
  <div :class="containerClasses">
    <Listbox :model-value="modelValue" @update:model-value="$emit('update:modelValue', $event)" :disabled="disabled">
      <ListboxLabel v-if="buttonTitle" class="mb-2 font-semibold block">
        {{ buttonTitle }}
      </ListboxLabel>

      <div class="relative">
        <ListboxButton
          :class="buttonClasses"
          :aria-label="buttonTitle ? undefined : resolvedPlaceholder"
        >
          <span class="block truncate text-left">
            {{ selectedText || resolvedPlaceholder }}
          </span>

          <span
            class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3"
          >
            <ChevronUpDownIcon class="h-5 w-5 text-blue-600" />
          </span>
        </ListboxButton>

        <transition
          leave-active-class="transition duration-200 ease-in"
          leave-from-class="opacity-100"
          leave-to-class="opacity-0"
          enter-active-class="transition duration-200 ease-out"
          enter-from-class="opacity-0 scale-95"
          enter-to-class="opacity-100 scale-100"
        >
          <ListboxOptions
            class="absolute z-50 mt-2 max-h-60 w-full overflow-auto rounded-xl bg-white p-1 text-sm shadow-lg border border-slate-200"
          >
            <ListboxOption
              v-for="option in values"
              :key="option.id ?? option.name"
              :value="option"
              v-slot="{ active, selected }"
              as="template"
            >
              <li
                :class="[
                  active ? 'bg-slate-100' : '',
                  selected ? 'bg-blue-100' : '',
                  'relative cursor-pointer select-none py-2 px-3 rounded-lg transition-colors duration-150',
                ]"
              >
                <div class="flex items-center">
                  <div
                    v-if="selected"
                    class="h-5 w-5 mr-2 bg-blue-100 rounded-full flex items-center justify-center"
                  >
                    <CheckIcon class="h-4 w-4 text-blue-700" />
                  </div>
                  <div v-else class="h-4 w-4 mr-2"></div>

                  <span
                    :class="[
                      selected
                        ? 'font-medium text-blue-700'
                        : 'font-normal text-slate-700',
                      'block truncate',
                    ]"
                  >
                    {{ option[labelKey] }}
                  </span>
                </div>
              </li>
            </ListboxOption>
          </ListboxOptions>
        </transition>
      </div>
    </Listbox>

    <!-- Helper Text -->
    <div
      v-if="!!$slots.helper"
      class="mt-2 text-sm text-slate-500 flex gap-1.5"
    >
      <InformationCircleIcon class="h-4 w-4 mt-0.5 flex-shrink-0" />
      <div><slot name="helper"></slot></div>
    </div>
  </div>
</template>

<script>
import {
  Listbox,
  ListboxButton,
  ListboxLabel,
  ListboxOptions,
  ListboxOption,
} from "@headlessui/vue";

import {
  ChevronUpDownIcon,
  CheckIcon,
  InformationCircleIcon,
} from "@heroicons/vue/20/solid";

export default {
  name: "Dropdown",

  components: {
    Listbox,
    ListboxButton,
    ListboxLabel,
    ListboxOptions,
    ListboxOption,
    ChevronUpDownIcon,
    CheckIcon,
    InformationCircleIcon,
  },

  props: {
    modelValue: {
      type: Object,
      default: null,
    },
    values: {
      type: Array,
      required: true,
    },
    placeholder: {
      type: String,
      default: undefined,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    size: {
      type: String,
      default: "md",
      validator: (value) => ["sm", "md", "lg"].includes(value),
    },
    width: {
      type: String,
      default: "full",
      validator: (value) => ["full", "fit"].includes(value),
    },
    buttonTitle: {
      type: String,
      default: undefined,
    },
    valueKey: {
      type: String,
      default: "id",
    },
    labelKey: {
      type: String,
      default: "name",
    },
  },

  emits: ["update:modelValue"],

  computed: {
    resolvedPlaceholder() {
      return this.placeholder ?? "Bitte auswählen";
    },

    sizeClasses() {
      return {
        sm: "h-7 px-3 pr-10 text-xs",
        md: "h-9 px-4 pr-12 text-sm",
        lg: "h-11 px-5 pr-14 text-base",
      };
    },

    selectedText() {
      if (!this.modelValue) return "";

      const found = this.values.find((v) => this.isSame(v, this.modelValue));
      return found?.[this.labelKey] ?? "";
    },

    containerClasses() {
      return ["text-sm", this.width === "fit" ? "inline-block" : ""].join(" ");
    },

    buttonClasses() {
      const base =
        "relative cursor-pointer rounded-full bg-slate-200 text-slate-800 text-left transition-all duration-200 shadow focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 border-blue-200 border";

      const width = this.width === "full" ? "w-full" : "w-auto";

      const disabled = this.disabled
        ? "opacity-50 cursor-not-allowed"
        : "hover:bg-slate-300";

      return [base, width, this.sizeClasses[this.size], disabled].join(" ");
    },
  },

  methods: {
    isSame(a, b) {
      const key = this.valueKey;
      return a?.[key] !== undefined && b?.[key] !== undefined
        ? a[key] === b[key]
        : JSON.stringify(a) === JSON.stringify(b);
    },
  },
};
</script>