<template>
  <div
    v-if="props.gql.currentProject?.currentTestingType"
    class="mx-auto text-center max-w-642px py-40px"
  >
    <div class="m-x-auto max-w-600px">
      <h1
        data-cy="create-spec-page-title"
        class="text-gray-900 mb-12px text-32px"
      >
        {{ props.title }}
      </h1>
      <p
        data-cy="create-spec-page-description"
        class="leading-normal text-gray-600 mb-32px text-18px"
      >
        <i18n-t
          scope="global"
          :keypath="descriptionKeyPath"
        >
          <OpenConfigFileInIDE
            v-if="props.gql.currentProject.configFileAbsolutePath"
            v-slot="{onClick}"
            :gql="props.gql.currentProject"
          >
            <button
              class="text-purple-500 hocus-link-default"
              data-cy="no-specs-specPattern"
              @click="onClick"
            >
              specPattern
            </button>
          </OpenConfigFileInIDE>
          <span v-else>specPattern</span>
        </i18n-t>
      </p>
    </div>

    <DefaultSpecPatternNoContent
      v-if="props.isDefaultSpecPattern"
      :gql="props.gql"
      @showCreateSpecModal="showCreateSpecModal"
    />
    <CustomPatternNoSpecContent
      v-else
      :gql="props.gql.currentProject"
      @showCreateSpecModal="showCreateSpecModal"
    />
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue'
import DefaultSpecPatternNoContent from './DefaultSpecPatternNoContent.vue'
import { gql } from '@urql/vue'
import type { NoSpecsPageFragment } from '../generated/graphql'
import CustomPatternNoSpecContent from './CustomPatternNoSpecContent.vue'
import OpenConfigFileInIDE from '@packages/frontend-shared/src/gql-components/OpenConfigFileInIDE.vue'

gql`
fragment NoSpecsPage on Query {
  ...CreateSpecCards
  ...ChooseExternalEditor
  currentProject {
    id
    currentTestingType
    configFileAbsolutePath
    ...CustomPatternNoSpecContent
    ...OpenConfigFileInIDE
  }
}
`

const props = defineProps<{
  gql: NoSpecsPageFragment
  title: string
  isDefaultSpecPattern: boolean
}>()

const emit = defineEmits<{
  (e: 'showCreateSpecModal', id?: string): void
}>()

const showCreateSpecModal = (id?: string) => {
  emit('showCreateSpecModal', id)
}

const descriptionKeyPath = computed(() => {
  return props.isDefaultSpecPattern ?
    `createSpec.page.defaultPatternNoSpecs.${props.gql.currentProject?.currentTestingType}.description` :
    'createSpec.page.customPatternNoSpecs.description'
})

</script>
