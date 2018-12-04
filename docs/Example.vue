<template>
  <sui-grid
    :class="containerClass"
    :columns="1"
    divided
  >
    <sui-grid-column>
      <h3 class="example-header" is="sui-header" :id="kebabCase(title)">
        {{ title }}
      </h3>
      <p>{{ description }}</p>
      <sui-menu
        class="options-menu"
        color="green"
        icon
        compact
        small
        text
      >
        <a is="sui-menu-item">
          <sui-icon
            color="grey"
            fitted
            name="linkify"
            size="large"
          />
        </a>
        <a is="sui-menu-item">
          <sui-icon
            color="grey"
            fitted
            name="window maximize"
            size="large"
          />
        </a>
        <a
          is="sui-menu-item"
          :active="showHtml"
          @click.native="showHtml = !showHtml"
        >
          <sui-icon
            :color="htmlColor"
            fitted
            name="html5"
            size="large"
          />
        </a>
        <a
          is="sui-menu-item"
          :active="showCode"
          @click.native="showCode = !showCode"
        >
          <sui-icon
            :color="codeColor"
            fitted
            name="code"
            size="large"
          />
        </a>
      </sui-menu>
    </sui-grid-column>

    <sui-grid-column v-if="info">
      <sui-message :info="!!info" v-markdown="info" />
    </sui-grid-column>

    <sui-grid-column :class="exampleClass">
      <renderer :scope="scope" :value="text" />
    </sui-grid-column>

    <sui-grid-column v-if="showCode">
      <sui-divider fitted horizontal>
        <sui-menu color="green" text>
          <a is="sui-menu-item" :active="copied" @click.native="copySource">
            <sui-icon name="copy" />
            <template v-if="copied">Copied!</template>
            <template v-if="!copied">Copy</template>
          </a>
          <a is="sui-menu-item" @click.native="resetSource">
            <sui-icon name="refresh" />
            Reset
          </a>
          <a is="sui-menu-item" :href="link">
            <sui-icon name="github" />
            Edit
          </a>
          <a
            is="sui-menu-item"
            href="https://github.com/Semantic-UI-Vue/Semantic-UI-Vue/issues/new"
          >
            <sui-icon name="bug" />
            Issue
          </a>
        </sui-menu>
      </sui-divider>
      <editor v-model="text" />
    </sui-grid-column>

    <sui-grid-column v-if="showHtml">
      <sui-divider horizontal>rendered html</sui-divider>
    </sui-grid-column>
  </sui-grid>
</template>

<script>
/* eslint-disable */
import upperFirst from 'lodash/upperFirst';
import camelCase from 'lodash/camelCase';
import kebabCase from 'lodash/kebabCase';
import { html } from 'js-beautify';
import copyToClipboard from 'copy-to-clipboard';
import * as SemanticUIVue from 'src';
import Editor from './Playground/Editor';
import Renderer from './Playground/Renderer';
import getComponentFromString from './getComponentFromString';

const parser = require('vue-loader/lib/parser');

export default {
  name: 'Example',
  components: { Editor, Renderer },
  props: {
    title: String,
    description: String,
    component: String,
    baseUrl: String,
    info: String,
  },
  data() {
    return {
      text: this.component,
      containerClass: '',
      copied: false,
      kebabCase() {},
      link: '',
      showCode: false,
      showHtml: false,
      codeColor: 'black',
      exampleClass: 'example',
      htmlColor: 'black',
      scope: { SemanticUIVue },
    }
  },
};
</script>

<style scoped>
.example-container {
  position: relative;
  margin-bottom: 2em !important;
  padding-bottom: 1em !important;
  transition: box-shadow 300ms;
}

.example-container.active {
  box-shadow: rgb(204, 204, 204) 0px 0px 30px;
}

.options-menu {
  transition: opacity 200ms;
  position: absolute;
  top: 1rem;
  right: 1rem;
  opacity: 0;
}

.example-container:hover .options-menu {
  opacity: 1;
}

.example-header {
  margin: 0!important;
}
</style>

<style>
.vuep .vuep-editor {
  display: none;
}

.vuep .vuep-error,
.vuep .vuep-preview {
  width: 100%;
}
</style>
