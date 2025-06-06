<script setup lang="ts">
import { ref, watch, computed } from "vue";
import Input from "@/components/controls/input.vue";
import Select from "@/components/controls/select.vue";
import Switch from "@/components/controls/switch.vue";
import { ListStyle, ButtonStyle, Trigger, LightMode, PastDateHandling, Size, Option } from "@/models/addToCalendarButton";
import { LanguageCode, LanguageNames } from "@/models/language";
import { LayoutAttrsKey, HideIconOption, HideTextOption } from "@/models/attrs";
import { getDefaultLayoutAttrs } from "@/utils/attrs";

const { t } = useI18n();

const props = defineProps({
  modelValue: {
    type: Object,
    default: getDefaultLayoutAttrs()
  },
  disabled: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(['update:modelValue']);

const internalValue = ref(props.modelValue || getDefaultLayoutAttrs());

watch(internalValue, () => {
  emit('update:modelValue', internalValue.value);
}, { deep: true });

watch(() => props.modelValue, (val: any) => {
  internalValue.value = val;
}, { immediate: true });

const languageOptions = computed(() =>
  Object.values(LanguageCode).map((item) =>
    ({
      key: LanguageNames[item] +  ` (${item})`,
      value: item
    })
  )
)

const triggerOptions = computed(() =>
  Object.values(Trigger).map((item: string) =>
    ({
      key: t(`options.trigger.${item}`.toLowerCase()),
      value: item
    })
  )
)

const i18nHideIconOptions = computed(() =>
  Object.keys(HideIconOption).map((key: string) =>
    ({
      key: t(`options.hide_icon.${key}`.toLowerCase()),
      value: HideIconOption[key as keyof typeof HideIconOption]
    })
  )
)

const i18nHideTextOptions = computed(() =>
  Object.keys(HideTextOption).map((key: string) =>
    ({
      key: t(`options.hide_text.${key}`.toLowerCase()),
      value: HideTextOption[key as keyof typeof HideTextOption]
    })
  )
)

function onHideIconOptionsUpdate(options: string[]) {
  Object.keys(internalValue.value[LayoutAttrsKey.HIDE_ICON_OPTIONS]).forEach((key) => {
    internalValue.value[LayoutAttrsKey.HIDE_ICON_OPTIONS][key] = options.includes(key);
  });
}

function onHideTextOptionsUpdate(options: string[]) {
  Object.keys(internalValue.value[LayoutAttrsKey.HIDE_TEXT_OPTIONS]).forEach((key) => {
    internalValue.value[LayoutAttrsKey.HIDE_TEXT_OPTIONS][key] = options.includes(key);
  });
}

const optionsOptions = computed(() => Object.values(Option).map(item => ({ key: item, value: item })));
const buttonStyleOptions = computed(() => Object.values(ButtonStyle).map(item => ({ key: item, value: item })));
const listStyleOptions = computed(() => Object.values(ListStyle).map(item => ({ key: item, value: item })));
const lightModeOptions = computed(() => Object.values(LightMode).map(item => ({ key: item, value: item })));
const pastDateHandlingOptions = computed(() => Object.values(PastDateHandling).map(item => ({ key: item, value: item })));
</script>

<template>
  <div class="grid">
    <Select v-model="internalValue[LayoutAttrsKey.OPTIONS]" :disabled="disabled" multiselect :label="$t(`labels.inputs.${[LayoutAttrsKey.OPTIONS]}`.toLocaleLowerCase())" :options="optionsOptions" byKey="key" byValue="value" class="z-20 mb-3 border-b border-zinc-400 pb-4 dark:border-zinc-600" />

    <div class="z-10 mb-3 flex items-center justify-between">
      <Select v-model="internalValue[LayoutAttrsKey.BUTTON_STYLE]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.BUTTON_STYLE]}`.toLocaleLowerCase())" :options="buttonStyleOptions" byKey="key" byValue="value" class="mr-5 flex-1" />
      <Switch v-model="internalValue[LayoutAttrsKey.IS_BUTTONS_LIST]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.IS_BUTTONS_LIST]}`.toLocaleLowerCase())" class="pb-0 pt-6" />
    </div>
    <Input v-model="internalValue[LayoutAttrsKey.LABEL]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.LABEL]}`.toLocaleLowerCase())" type="text" class="mb-3" />
    <Select v-model="internalValue[LayoutAttrsKey.TRIGGER]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.TRIGGER]}`.toLocaleLowerCase())" :options="triggerOptions" byKey="key" byValue="value" class="mb-3" />

    <Select v-model="internalValue[LayoutAttrsKey.LIST_STYLE]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.LIST_STYLE]}`.toLocaleLowerCase())" :options="listStyleOptions" byKey="key" byValue="value" class="mb-3" />
    <Switch v-model="internalValue[LayoutAttrsKey.HIDE_BACKGROUND]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.HIDE_BACKGROUND]}`.toLocaleLowerCase())" class="mb-3" />

    <Select
      :modelValue="Object.keys(internalValue[LayoutAttrsKey.HIDE_ICON_OPTIONS] ? internalValue[LayoutAttrsKey.HIDE_ICON_OPTIONS] : {}).filter((key) => !!internalValue[LayoutAttrsKey.HIDE_ICON_OPTIONS][key])"
      :disabled="disabled"
      :label="$t(`labels.inputs.${[LayoutAttrsKey.HIDE_ICON_OPTIONS]}`.toLocaleLowerCase())"
      :options="i18nHideIconOptions"
      byKey="key"
      byValue="value"
      multiselect
      class="mb-3"
      @update:model-value="onHideIconOptionsUpdate"
    />
    <Select
      :modelValue="Object.keys(internalValue[LayoutAttrsKey.HIDE_TEXT_OPTIONS] ? internalValue[LayoutAttrsKey.HIDE_TEXT_OPTIONS] : {}).filter((key) => !!internalValue[LayoutAttrsKey.HIDE_TEXT_OPTIONS][key])"
      :disabled="disabled"
      :label="$t(`labels.inputs.${[LayoutAttrsKey.HIDE_TEXT_OPTIONS]}`.toLocaleLowerCase())"
      :options="i18nHideTextOptions"
      byKey="key"
      byValue="value"
      multiselect
      class="mb-3"
      @update:model-value="onHideTextOptionsUpdate"
    />
    <Switch v-model="internalValue[LayoutAttrsKey.HIDE_CHECKMARK]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.HIDE_CHECKMARK]}`.toLocaleLowerCase())" class="mb-3" />

    <Input v-model="internalValue[LayoutAttrsKey.SIZE]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.SIZE]}`.toLocaleLowerCase())" type="range" :min="Size.min" :max="Size.max" class="mb-3" />

    <Select v-model="internalValue[LayoutAttrsKey.LIGHT_MODE]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.LIGHT_MODE]}`.toLocaleLowerCase())" :options="lightModeOptions" byKey="key" byValue="value" class="mb-3" />

    <Select v-model="internalValue[LayoutAttrsKey.PAST_DATE_HANDLING]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.PAST_DATE_HANDLING]}`.toLocaleLowerCase())" :options="pastDateHandlingOptions" byKey="key" byValue="value" class="mb-3" />

    <Select v-model="internalValue[LayoutAttrsKey.LANGUAGE]" :disabled="disabled" :label="$t(`labels.inputs.${[LayoutAttrsKey.LANGUAGE]}`.toLocaleLowerCase())" :options="languageOptions" byKey="key" byValue="value" />
  </div>
</template>
