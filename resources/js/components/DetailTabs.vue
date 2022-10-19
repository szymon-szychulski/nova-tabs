<template>
  <div :class="darkModeClass">
    <div class="tab-group">
      <slot>
        <Heading :level="1" v-text="panel.name" />

        <p
          v-if="panel.helpText"
          class="text-gray-500 text-sm font-semibold italic"
          :class="panel.helpText ? 'mt-2' : 'mt-3'"
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
                  :dusk="tab.slug + '-tab'"
                  :class="getIsTabCurrent(tab) ? 'active tabs-text-' + getCurrentColor() + '-500 tabs-font-bold tabs-border-b-2 tabs-border-b-' + getCurrentColor() + '-500' : 'tabs-text-gray-600 hover:tabs-text-gray-800 dark:tabs-text-gray-400 hover:dark:tabs-text-gray-200'"
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
            :key="'related-tabs-fields' + index"
            :ref="getTabRefName(tab)"
            :class="[
                        'tab',
                        tab.slug,
                        tab.classes
                    ]"
            :label="tab.name"
            v-show="getIsTabCurrent(tab)"
        >
          <div :class="getBodyClass(tab)">
            <Card
              v-if="tab.fields.filter(field => getComponentName(field) != 'detail-has-many-field').length > 0"
              class="mt-3 py-2 px-6 divide-y divide-gray-100 dark:divide-gray-700"
            >
              <KeepAlive
                v-for="(field, index) in tab.fields.filter(field => getComponentName(field) != 'detail-has-many-field')"
                :key="index"
              >
                <component
                  :is="getComponentName(field)"
                  :field="field"
                  :index="index"
                  :resource="resource"
                  :resource-id="resourceId"
                  :resource-name="resourceName"
                  @actionExecuted="actionExecuted"
                />
              </KeepAlive>
            </Card>

            <div
              v-if="tab.fields.filter(field => getComponentName(field) === 'detail-has-many-field').length > 0"
              class="mt-6"
            >
              <KeepAlive
                v-for="(field, index) in tab.fields.filter(field => getComponentName(field) === 'detail-has-many-field')"
                :key="index"
              >
                  <div class="mb-8">
                    <component
                      :is="getComponentName(field)"
                      :field="field"
                      :index="index"
                      :resource="resource"
                      :resource-id="resourceId"
                      :resource-name="resourceName"
                      @actionExecuted="actionExecuted"
                    />
                  </div>
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
      default: 'detail',
    },
  }
};
</script>
