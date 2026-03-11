<script setup>
import AddLink from '@/components/icons/AddLink.vue'
import useDialogStore from 'stores/dialog.js'
import { NButton } from 'naive-ui'

const dialogStore = useDialogStore()
</script>

<template>
    <div class="content-container flex-box-v">
        <!-- TODO: replace icon to app icon -->
        <n-empty :description="$t('interface.empty_server_content')">
            <template #extra>
                <n-button :focusable="false" @click="dialogStore.openNewDialog()">
                    <template #icon>
                        <n-icon :component="AddLink" size="18" />
                    </template>
                    {{ $t('interface.new_conn') }}
                </n-button>
            </template>
        </n-empty>
    </div>
</template>

<style lang="scss" scoped>
@use '@/styles/content';

.content-container {
    justify-content: center;
    padding: 5px;
    box-sizing: border-box;
}
</style>
