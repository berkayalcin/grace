<template>
  <date-picker
    class="g-date-picker"
    v-bind="getAttrs"
    :lang="lang"
    :show-week-number="showWeekNumber"
    :is-range-mode="isRangeMode"
    v-model="date"
    @input="onInput"
    v-on="listeners"
  >
    <template slot="input">
      <g-input
        class="picker-input"
        v-model="getDate"
        :label="getPlaceholder"
        :disabled="true"
        readonly
        v-bind="getInputAttrs"
      />
    </template>

    <template
      v-for="(_, name) in $scopedSlots"
      v-slot:[name]="{emit}"
    >
      <slot
        :name="name"
        :emit="emit"
      />
    </template>
  </date-picker>
</template>

<script lang="ts">

import DatePicker from 'vue2-datepicker';
import GInput from '../GInput/GInput.vue';
import dayjs from 'dayjs';
import 'dayjs/locale/tr';
import 'vue2-datepicker/locale/tr';

const _dateFormat = (value:any, format:any) => {
  if (!value) {
    return null;
  }
  const date = dayjs(value);
  return date.isValid() ? date.format(format) : value;
};
export default {
  name: 'GDatePicker',
  props: {
    value: {
      type: [String, Date, Array],
      default: () => null,
    },
    type: {
      type: String,
      default: () => 'date',
    },
    showWeekNumber: {
      type: Boolean,
      default: () => false,
    },
    isRangeMode: {
      type: Boolean,
      default: () => false,
    },
  },
  components: {
    DatePicker,
    GInput,
  },
  data () {
    return {
      lang: {
        formatLocale: {
          firstDayOfWeek: 1,
        },
        monthBeforeYear: false,
      },
      date: null,
    };
  },
  computed: {
    listeners (): any {
      const { input, ...rest } = this.$listeners;

      return rest;
    },
    getDate: {
      get () {
        if (this.type === 'week') {
          const startDate = dayjs(this.date).locale('tr').startOf('week').format('DD.MM.YYYY');
          const endDate = dayjs(this.date).locale('tr').endOf('week').format('DD.MM.YYYY');
          return startDate + ' - ' + endDate;
        }
        if (!this.date) {
          return null;
        } else if (this.date instanceof Array) {
          return this.date.map(item => _dateFormat(item, this.getAttrs.format))
            .join(this.getAttrs.separator);
        }
        return _dateFormat(this.date, this.getAttrs.format);
      },
    },
    getAttrs () {
      return {
        ...{
          type: this.type,
          format: 'DD.MM.YYYY',
          placeholder: 'Başlangıç Tarihi',
          'append-to-body': false,
          separator: ' - ',
        },
        ...this.$attrs,
      };
    },
    getInputAttrs () {
      const { disable, success, error, size, feedback } = this.getAttrs;
      return { disable, success, error, size, feedback };
    },
    getPlaceholder () {
      return this.getAttrs.placeholder;
    },
  },
  methods: {
    onInput (date) {
      this.$emit('input', date);
    },
  },
  watch: {
    value: {
      handler (newValue) {
        this.date = newValue;
      },
      immediate: true,
    },
  },
};
</script>

<style lang="scss" scoped>
  @import '~vue2-datepicker/index.css';
  .g-date-picker{
    ::v-deep .picker-input input {
      cursor: auto;
    }
    &[error]{
      ::v-deep .mx-icon-calendar svg{
        stroke: var(--red-500);
        fill: white;
      }
      ::v-deep .mx-icon-clear svg{
        stroke: var(--red-500);
      }
    }
    &[success]{
      ::v-deep .mx-icon-calendar svg{
        fill: var(--green-500);
      }
      ::v-deep .mx-icon-clear svg{
        fill: var(--green-500);
      }
    }
    &[disabled]{
      ::v-deep .mx-icon-calendar svg{
        fill: var(--dark-grey-500);
      }
      ::v-deep .mx-icon-clear svg{
        fill: var(--dark-grey-500);
      }
    }
    ::v-deep{
      .g-field-wrapper{
        margin-bottom: 0;
      }
      .mx-datepicker-main {
        .mx-datepicker-header {
          display: flex;
          flex-wrap: wrap;
          padding: 6px 8px;
          border-bottom: 1px solid #e8e8e8;
          gap: 3px;
        }
        .mx-calendar-header{
          .mx-btn{
            color: var(--mid-grey-500);
          }
          .mx-calendar-header-label{
            .mx-btn{
              color: var(--dark-grey-500);
            }
          }
        }
        .mx-datepicker-footer {
          display: flex;
          flex-wrap: wrap;
          flex-direction: row-reverse;
          padding: 6px 8px;
          border-bottom: 1px solid #e8e8e8;
          gap: 3px;
        }
        .mx-calendar-footer{
          .mx-btn{
            color: var(--mid-grey-500);
          }
          .mx-calendar-footer-label{
            .mx-btn{
              color: var(--dark-grey-500);
            }
          }
        }
        .mx-calendar-week-mode{
          .mx-calendar-content{
            .mx-date-row{
              &:hover{
                color: var(--orange-500);
                background-color: var(--orange-900);
              }
            }
            .mx-active-week{
              background-color: var(--orange-500);
              color: var(--white);
            }
          }
        }
        .mx-calendar-content{
          .mx-table {
            .today {
              color: var(--orange-500);
            }
            .cell{
              &.not-current-month{
                color: var(--light-grey-500);
              }
              &.in-range,
              &:hover{
                color: var(--orange-500);
                background-color: var(--orange-900);
              }
              &.active{
                background-color: var(--orange-500);
                color: var(--white);
              }
            }
            td, th{
              vertical-align: middle;
            }
          }
        }
        .mx-time-content{
          .mx-time-item{
            &.active{
              color: var(--orange-500);
            }
            &:hover{
              color: var(--orange-500);
              background-color: var(--orange-900);
            }
          }
        }
      }
    }
  }
</style>
