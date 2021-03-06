<template>
  <div
    :class="classes.root"
  >
    <div
      :class="classes.label"
      @click="toggleDropdown()"
    >
      <div :class="classes.labelTitle">
        <span>
          {{ title }}
        </span>
        
        <span
          v-if="props.modelValue && props.modelValue.length > 0 && props.multiple"
          :class="classes.labelCounter"
        >
          <span
            :class="classes.labelCounterNumber"
          >
            {{ modelValue.length }}
          </span>
        </span>
        
        <span
          v-if="props.modelValue && !props.multiple"
          :class="classes.labelCounterIcon"
        />
      </div>
      
      <ChevronDownIcon :class="[...classes.labelChevronIcon, ...conditionalClasses(isDropdownOpen, classes.labelChevronIconOpen) ]" />
    </div>
    
    <div
      v-if="isDropdownOpen"
      :class="classes.dropdown"
    >
      <div :class="classes.options">
        <div
          v-for="option in props.options"
          :key="getOptionValue(option)"
          :class="classes.option"
          @click="toggleSelectOption(option)"
        >
          <div :class="classes.optionLabel">
            {{ getOptionLabel(option) }}
          </div>
          
          <CheckIcon
            v-if="isOptionActive(option)"
            :class="classes.optionIcon"
          />
          <div
            v-if="!isOptionActive(option)"
            :class="classes.optionIcon"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, reactive, ref } from 'vue';
import { directive as vClickOutside } from 'click-outside-vue3';
import { ChevronDownIcon, CheckCircleIcon, CheckIcon } from '@heroicons/vue/solid';
import defaultClasses from '../defaultClasses';
import { Classes } from '../types/Classes';

interface Props {
  multiple?: boolean;
  options: any[];
  title: string;
  modelValue: any;
  optionLabel: string;
  optionValue: string;
  classes?: Classes;
}

interface Emits {
  (e: 'update:modelValue', modelValue: Props['modelValue']): void;
}

const props = defineProps<Props>();
const emit = defineEmits<Emits>();

function conditionalClasses(condition: boolean, classes: string[]): string[] {
  return condition ? classes : [];
}

// const classes = reactive<Classes>(defaultClasses);
//
// if (props.classes) {
//   Object.assign(classes, props.classes);
// }

const isDropdownOpen = ref(false);

function openDropdown() {
  isDropdownOpen.value = true;
}

function closeDropdown() {
  isDropdownOpen.value = false;
}

function toggleDropdown() {
  isDropdownOpen.value = !isDropdownOpen.value;
}

function getOptionLabel(option): string {
  return option[props.optionLabel];
}

function getOptionValue(option): string | number {
  return option[props.optionValue];
}

const sortedOptions = computed(() => {
  return [];
});

function isOptionActive(option) {
  const selected = props.modelValue;
  const optionValue = getOptionValue(option);
  
  if (!props.multiple) {
    return selected === optionValue;
  } else {
    if (Array.isArray(selected)) {
      return selected.indexOf(optionValue) > -1;
    } else {
      return false;
    }
  }
}

function toggleSelectOption(option) {
  // closeDropdown()
  let selected = props.modelValue;
  const optionValue = getOptionValue(option);
  
  if (!props.multiple) {
    // if single option result
    
    if (optionValue !== selected) {
      emit('update:modelValue', optionValue);
    } else {
      emit('update:modelValue', null);
    }
  } else {
    // if multiply options result
    
    if (!Array.isArray(selected)) {
      selected = [];
    }
    
    const selectedIndex = selected.indexOf(optionValue);
    
    if (selectedIndex > -1) {
      // remove existing item
      selected.splice(selectedIndex, 1);
    } else {
      // add new item
      selected.push(optionValue);
    }
    
    emit('update:modelValue', selected);
  }
}

</script>

<style scoped>

</style>
