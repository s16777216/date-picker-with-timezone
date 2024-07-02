<script setup lang="ts">
import {ElDatePicker} from "element-plus";
import {computed, ref} from "vue";
interface DateWithTimezoneProps {
  modelValue?: string | [string, string];
  timezone?: string;
  onlyChangeTimezoneWhenEmpty?: boolean;
}
const props = withDefaults(defineProps<DateWithTimezoneProps>(), {
  onlyChangeTimezoneWhenEmpty: false,
});
const emit = defineEmits<{
  (event: 'update:modelValue', value: string | [string, string]): void;
  (event: 'update:timezone', value: string): void;
}>();
const _modelValue = computed({
  get: () => {
    if(!props.modelValue || !props.timezone) return props.modelValue;
    if (Array.isArray(props.modelValue)) {
      const start = props.modelValue[0];
      const end = props.modelValue[1];
      return [culLocalTime(start, props.timezone), culLocalTime(end, props.timezone)];
    }else{
      return culLocalTime(props.modelValue, props.timezone);
    }
  },
  set: (val) => {
    if(!props.timezone || !val) return val;
    let result;
    if (Array.isArray(val)) {
      const start = combineDateAndTimezone(val[0], props.timezone);
      const end = combineDateAndTimezone(val[1], props.timezone);
      result = [start, end] as [string, string];
    }else{
      result = combineDateAndTimezone(val, props.timezone);
    }
    emit('update:modelValue', result);
  },
})
const onDateChange = () => {
  if(props.onlyChangeTimezoneWhenEmpty) {
    if (!props.timezone) {
      emit('update:timezone', getCurrentTimezone());
    }
  }else{
    emit('update:timezone', getCurrentTimezone());
  }
}
const getCurrentTimezone = () => {
  const date = new Date();
  const offset = date.getTimezoneOffset();
  const offsetHours = Math.abs(offset / 60);
  const offsetMinutes = Math.abs(offset % 60);
  const offsetSign = offset < 0 ? '+' : '-';
  return `${offsetSign}${offsetHours.toString().padStart(2, '0')}:${offsetMinutes.toString().padStart(2, '0')}`;
}
const culLocalTime = (date: string, timezone: string) => {
  // date: 2024-07-02T12:00:00.000+08:00, timezone: +05:30 => 2024-07-02T09:30:00.000
  const dateObj = new Date(date);
  const offset = timezoneToOffset(timezone);
  const localOffset = dateObj.getTimezoneOffset();
  const diff = localOffset - offset;
  dateObj.setMinutes(dateObj.getMinutes() + diff);
  const year = dateObj.getFullYear();
  const month = (dateObj.getMonth() + 1).toString().padStart(2, '0');
  const day = dateObj.getDate().toString().padStart(2, '0');
  const hour = dateObj.getHours().toString().padStart(2, '0');
  const minute = dateObj.getMinutes().toString().padStart(2, '0');
  const second = dateObj.getSeconds().toString().padStart(2, '0');
  const millisecond = dateObj.getMilliseconds().toString().padStart(3, '0');
  const result = `${year}-${month}-${day}T${hour}:${minute}:${second}.${millisecond}`;
  console.log(result)
  return result
}
const combineDateAndTimezone = (date: string, timezone: string) => {
  return `${date}${timezone}`;
}
const timezoneToOffset = (timezone: string) => {
  const sign = timezone[0] === '-' ? 1 : -1;
  const offsetHour = parseInt(timezone.slice(1, 3));
  const offsetMinutes = parseInt(timezone.slice(4, 6));
  return sign * (offsetHour * 60 + offsetMinutes);
}
</script>
<style scoped>
.edptz {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
<template>
  <div class="edptz">
    <el-date-picker
        v-model="_modelValue"
        type="datetime"
        @change="onDateChange"
        value-format="YYYY-MM-DDTHH:mm:ss.SSS"
    />
    <p>TZ: {{ props.timezone }}</p>
  </div>
</template>

