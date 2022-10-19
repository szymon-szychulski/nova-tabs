<template>
  <div :class="darkModeClass">
    <div class="tab-group">
      <slot>
        <Heading :level="1" :class="panel.helpText ? 'mb-2' : 'mb-3'" v-text="panel.name"/>

        <p
          v-if="panel.helpText"
          class="text-gray-500 text-sm font-semibold italic mb-3"
          v-html="panel.helpText"
        ></p>
      </slot>

      <div class="tab-card">
        <div id="tabs">
          <div class="block">
            <nav
              aria-label="Tabs"
              class="tab-menu"
            >
              <a
                v-for="(tab, key) in getSortedTabs(tabs)"
                :key="key"
                :class="getIsTabCurrent(tab) ? 'active tabs-text-' + getCurrentColor() + '-500 tabs-font-bold tabs-border-b-2 tabs-border-b-' + getCurrentColor() + '-500' : 'tabs-text-gray-600 hover:tabs-text-gray-800 dark:tabs-text-gray-400 hover:dark:tabs-text-gray-200'"
                :dusk="tab.slug + '-tab'"
                :ref="tab.slug + '-tab'"
                class="tab-item border-gray-200"
                @click.prevent="handleTabClick(tab)"
              >
                <span class="capitalize">{{ tab.properties.title }}</span>
              </a>
            </nav>
          </div>
        </div>

        <div
          v-for="(tab, index) in getSortedTabs(tabs)"
          v-show="getIsTabCurrent(tab)"
          :key="'related-tabs-fields' + index"
          :ref="getTabRefName(tab)"
          :class="[
                        'tab fields-tab',
                        getIsTabCurrent(tab) ? 'block' : 'hidden',
                        tab.slug,
                    ]"
          :label="tab.name"
        >
          <div :class="getBodyClass(tab)">
            <Card
              v-if="tab.fields.filter(field => getComponentName(field) != 'form-has-one-field').length > 0"
              class="mt-3 py-2 divide-y divide-gray-100 dark:divide-gray-700"
            >
              <KeepAlive>
                <template
                  v-for="(field, index) in tab.fields.filter(field => getComponentName(field) != 'form-has-one-field')"
                  :key="'tab-' + index"
                >
                  <component
                    v-if="!field.from"
                    :is="getComponentName(field)"
                    ref="fields"
                    :errors="validationErrors"
                    :field="field"
                    :form-unique-id="formUniqueId"
                    :related-resource-id="relatedResourceId"
                    :related-resource-name="relatedResourceName"
                    :resource-id="resourceId"
                    :resource-name="resourceName"
                    :show-help-text="field.helpText != null"
                    :shown-via-new-relation-modal="shownViaNewRelationModal"
                    :via-relationship="viaRelationship"
                    :via-resource="viaResource"
                    :via-resource-id="viaResourceId"
                    @field-changed="$emit('field-changed')"
                    @file-deleted="$emit('update-last-retrieved-at-timestamp')"
                    @file-upload-started="$emit('file-upload-started')"
                    @file-upload-finished="$emit('file-upload-finished')"
                  />

                  <component
                      v-if="field.from"
                      :is="getComponentName(field)"
                      :errors="validationErrors"
                      :resource-id="getResourceId(field)"
                      :resource-name="field.resourceName"
                      :field="field"
                      :via-resource="field.from.viaResource"
                      :via-resource-id="field.from.viaResourceId"
                      :via-relationship="field.from.viaRelationship"
                      :form-unique-id="relationFormUniqueId"
                      @field-changed="$emit('field-changed')"
                      @file-deleted="$emit('update-last-retrieved-at-timestamp')"
                      @file-upload-started="$emit('file-upload-started')"
                      @file-upload-finished="$emit('file-upload-finished')"
                      :show-help-text="field.helpText != null"
                  />
                </template>
              </KeepAlive>
            </Card>

            <div
              v-if="tab.fields.filter(field => getComponentName(field) === 'form-has-one-field').length > 0"
              class="mt-6"
            >
              <KeepAlive>
                <template
                  v-for="(field, index) in tab.fields.filter(field => getComponentName(field) === 'form-has-one-field')"
                  :key="'tab-' + index"
                >
                  <div class="mb-8">
                    <Heading :level="4" :class="field.helpText ? 'mb-2' : 'mb-3'">
                      {{field.name}}
                    </Heading>

                    <p
                      v-if="field.helpText"
                      class="text-gray-500 text-sm font-semibold italic mb-3"
                      v-html="field.helpText"
                    ></p>

                    <component
                      v-if="!field.from"
                      :is="getComponentName(field)"
                      ref="fields"
                      :errors="validationErrors"
                      :field="field"
                      :form-unique-id="formUniqueId"
                      :related-resource-id="relatedResourceId"
                      :related-resource-name="relatedResourceName"
                      :resource-id="resourceId"
                      :resource-name="resourceName"
                      :show-help-text="field.helpText != null"
                      :shown-via-new-relation-modal="shownViaNewRelationModal"
                      :via-relationship="viaRelationship"
                      :via-resource="viaResource"
                      :via-resource-id="viaResourceId"
                      @field-changed="$emit('field-changed')"
                      @file-deleted="$emit('update-last-retrieved-at-timestamp')"
                      @file-upload-started="$emit('file-upload-started')"
                      @file-upload-finished="$emit('file-upload-finished')"
                    />

                    <component
                      v-if="field.from"
                      :is="getComponentName(field)"
                      :errors="validationErrors"
                      :resource-id="getResourceId(field)"
                      :resource-name="field.resourceName"
                      :field="field"
                      :via-resource="field.from.viaResource"
                      :via-resource-id="field.from.viaResourceId"
                      :via-relationship="field.from.viaRelationship"
                      :form-unique-id="relationFormUniqueId"
                      @field-changed="$emit('field-changed')"
                      @file-deleted="$emit('update-last-retrieved-at-timestamp')"
                      @file-upload-started="$emit('file-upload-started')"
                      @file-upload-finished="$emit('file-upload-finished')"
                      :show-help-text="field.helpText != null"
                    />
                  </div>
                </template>
              </KeepAlive>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BehavesAsPanel from '../mixins/BehavesAsPanel';
import HasTabs from "../mixins/HasTabs";

export default {
  mixins: [BehavesAsPanel, HasTabs],
  props: {
    mode: {
      type: String,
      default: 'form',
    },
  }
};
</script>
