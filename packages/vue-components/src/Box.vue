<template>
  <div
    :class="[
      'alert box-container',
      containerStyle(),
      addClass,
      { 'd-flex': seamlessHeaderBool() },
    ]"
    :style="customStyle()"
  >
    <!--Icon and vertical divider for seamless header box-->
    <div v-if="seamlessHeaderBool()" class="d-flex">
      <div
        v-if="iconBool()"
        :class="['icon-wrapper', iconStyle()]"
        :style="customIconColorStyle()"
      >
        <slot name="icon">
          <i :class="['fas', getFontAwesomeIconStyle()]"></i>
        </slot>
      </div>
      <div
        class="vertical-divider"
        :class="getBootstrapAlertStyle()"
        aria-hidden="true"
      ></div>
    </div>

    <div class="header-and-body">
      <!-- Header wrapper, not rendered if there is no header attribute -->
      <div
        v-if="headerBool()"
        :class="['box-header-wrapper', { 'alert-dismissible-box': dismissible }]"
      >
        <!-- icon on the left of the header -->
        <div
          v-if="iconBool() && !seamlessHeaderBool()"
          :class="['icon-wrapper', iconStyle()]"
          :style="customIconColorStyle()"
        >
          <slot name="icon">
            <i :class="['fas', getFontAwesomeIconStyle()]"></i>
          </slot>
        </div>

        <!-- header -->
        <div class="box-header">
          <slot name="header"></slot>
        </div>

        <!-- dismiss button to the right of the header -->
        <button
          v-if="dismissible"
          type="button"
          class="btn-close close-with-heading"
          data-bs-dismiss="alert"
          aria-label="Close"
        >
        </button>
      </div>

      <!-- Body wrapper -->
      <div
        :class="[
          'box-body-wrapper',
          {
            'alert-dismissible-box': dismissible && !headerBool(),
            'box-body-wrapper-with-heading': headerBool(),
          },
        ]"
      >
        <!-- icon on the left, not shown if there is a header -->
        <div
          v-if="iconBool() && !headerBool()"
          :class="['icon-wrapper', iconStyle()]"
          :style="customIconColorStyle()"
        >
          <slot name="icon">
            <i :class="['fas', getFontAwesomeIconStyle()]"></i>
          </slot>
        </div>

        <!-- Icon -- content divider for seamless boxes without the header attribute -->
        <div
          v-if="seamlessNoHeaderBool()"
          class="vertical-divider"
          :class="getBootstrapAlertStyle()"
          aria-hidden="true"
        ></div>

        <!-- Content wrapper -->
        <div class="contents" :style="customColorStyle()">
          <slot></slot>
        </div>

        <!-- dismiss button on the right, not shown if there is a header -->
        <button
          v-if="dismissible && !headerBool()"
          type="button"
          class="btn-close"
          data-bs-dismiss="alert"
          aria-label="Close"
        >
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { classifyBootstrapStyle } from './utils/utils';

export default {
  props: {
    dismissible: {
      type: Boolean,
      default: false,
    },
    backgroundColor: {
      type: String,
      default: null,
    },
    borderColor: {
      type: String,
      default: null,
    },
    borderLeftColor: {
      type: String,
      default: null,
    },
    color: {
      type: String,
      default: null,
    },
    icon: {
      type: String,
      default: null,
    },
    iconSize: {
      type: String,
      default: null,
    },
    iconColor: {
      type: String,
      default: null,
    },
    type: {
      type: String,
      default: '',
    },
    addClass: {
      type: String,
      default: '',
    },
    light: {
      type: Boolean,
      default: false,
    },
    seamless: {
      type: Boolean,
      default: false,
    },
    noIcon: {
      type: Boolean,
      default: false,
    },
    noBackground: {
      type: Boolean,
      default: false,
    },
    noBorder: {
      type: Boolean,
      default: false,
    },
    noPageBreak: {
      type: Boolean,
      default: false,
    },
    theme: {
      type: String,
      default: '',
    },
  },
  computed: {
    bootStrapStyle() {
      return classifyBootstrapStyle(this.type, this.theme);
    },
  },
  methods: {
    isSeamless() {
      return !this.light && this.seamless;
    },
    seamlessNoHeaderBool() {
      return this.isSeamless() && !this.headerBool();
    },
    seamlessHeaderBool() {
      return this.isSeamless() && this.headerBool();
    },
    headerBool() {
      return !!this.$slots.header;
    },
    iconBool() {
      // this.$slots.icon() is either undefined or an object
      const isIconSlotFilled = !!this.$slots.icon;
      return (!this.noIcon && this.type) || isIconSlotFilled;
    },
    containerStyle() {
      let containerStyle;

      if (this.light) {
        containerStyle = `${this.getBootstrapBorderStyle()} alert-border-left`;
      } else if (this.seamless) {
        containerStyle = 'seamless';
      } else {
        containerStyle = this.getBootstrapAlertStyle();
      }

      if (this.noBackground) {
        containerStyle += ' no-background';
      }

      if (this.noBorder) {
        containerStyle += ' no-border';
      }

      if (this.noPageBreak) {
        containerStyle += ' no-page-break';
      }

      return containerStyle;
    },
    customStyle() {
      const style = {};
      if (this.backgroundColor) {
        style.backgroundColor = this.backgroundColor;
        style.borderColor = this.backgroundColor;
      }
      if (this.borderColor) {
        style.border = `1px solid ${this.borderColor}`;
      }
      if (this.borderLeftColor) {
        style.borderLeft = `5px solid ${this.borderLeftColor}`;
      }
      return style;
    },
    customColorStyle() {
      if (this.color) {
        return { color: this.color };
      }
      return {};
    },
    iconStyle() {
      let iconStyle = '';
      if (this.iconSize) {
        iconStyle += `fa-${this.iconSize}`;
      }

      if (this.light || this.seamless) {
        iconStyle += ` ${this.getBootstrapTextStyle()}`;
      }

      return iconStyle;
    },
    customIconColorStyle() {
      if (this.iconColor) {
        return { color: `${this.iconColor}!important` };
      }
      return {};
    },
    getBootstrapAlertStyle() {
      return `alert-${this.bootStrapStyle.style}`;
    },
    getBootstrapTextStyle() {
      return `text-${this.bootStrapStyle.style}`;
    },
    getBootstrapBorderStyle() {
      return `border-${this.bootStrapStyle.style}`;
    },
    getFontAwesomeIconStyle() {
      return this.bootStrapStyle.icon;
    },
  },
};
</script>

<style scoped>
    .box-container {
        padding: 0;
        border-radius: 6px;
    }

    .box-header-wrapper {
        display: flex;
        flex-direction: row;
        align-items: center;
        width: 100%;
        padding: 0.4rem 1.25rem 0.28rem;
        border-radius: 6px 6px 0 0;
    }

    .box-body-wrapper {
        display: flex;
        flex-direction: row;
        width: 100%;
        padding: 0.75rem 1.25rem;
    }

    .box-container.seamless > .header-and-body > .box-body-wrapper {
        padding: 0.75rem 0.5rem;
    }

    .contents {
        padding: 0 6px;
        width: 100%;
        min-width: 0;
    }

    .box-container.seamless > .header-and-body > div.box-body-wrapper > .contents {
        padding-left: 12px;
    }

    .heading {
        display: inline;
        float: right;
        font-weight: normal;
        color: inherit;
        background-color: rgb(240 240 240 / 60%);
        width: auto;
        padding: 3px 5px 4px;
        border-width: 0;
        border-radius: 0 6px;
        margin: -13px -27px 0 15px;
    }

    .box-body-wrapper-with-heading {
        padding-top: 0.5rem;
    }

    .alert-dismissible-box {
        padding-right: 4rem;
    }

    .box-header {
        font-weight: 500;
    }

    :deep(div.box-header > *) {
        margin-bottom: 0;
    }

    .icon-wrapper {
        display: inline;
        text-align: center;
        margin-right: 0.5em;
        min-width: 1em;
    }

    .close-with-heading {
        top: 0;
        right: 0;
        position: absolute;
        padding: 1rem;
    }

    .close-with-heading > span {
        vertical-align: text-top;
    }

    .alert-dismissible-box .btn-close {
        position: absolute;
        top: 0;
        right: 0;
        z-index: 2;
        padding: 1rem;
    }

    .contents > :last-child {
        margin-bottom: 0;
    }

    .alert-default {
        color: #24292e;
        background-color: #f6f8fa;
        border-color: #e8ebef;
    }

    .alert-border-left {
        background-color: #f9f8f8;
        border-left: solid;
        border-width: 0 0 0 5px;
    }

    .vertical-divider {
        width: 4px;
    }

    .horizontal-divider {
        margin: 0 auto;
        width: calc(100% - 2.5rem);
        height: 3px;
    }

    .no-background {
        background: none;
    }

    .no-border {
        border: none;
    }

    @media screen and (width <= 768px) {
        .box-header-wrapper {
            padding: 0.75rem 0.75rem 0.1rem;
        }

        .box-body-wrapper {
            padding: 0.75rem;
        }

        .contents {
            padding: 0;
        }
    }

    @media print {
        .no-page-break {
            page-break-inside: avoid;
        }
    }
</style>
