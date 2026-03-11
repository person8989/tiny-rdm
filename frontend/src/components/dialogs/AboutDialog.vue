<script setup>
import iconUrl from '@/assets/images/icon.png'
import useDialog from 'stores/dialog.js'
import usePreferencesStore from 'stores/preferences.js'
import { useThemeVars } from 'naive-ui'

const themeVars = useThemeVars()
const dialogStore = useDialog()
const prefStore = usePreferencesStore()
</script>

<template>
    <n-modal v-model:show="dialogStore.aboutDialogVisible" :show-icon="false" preset="dialog" transform-origin="center">
        <n-space :size="10" :wrap="false" :wrap-item="false" align="center" vertical>
            <n-avatar :size="120" :src="iconUrl" color="#0000"></n-avatar>
            <div class="about-app-title">Tiny RDM</div>
            <n-text>{{ prefStore.appVersion }}</n-text>
            <div :style="{ color: themeVars.textColor3 }" class="about-copyright">
                Copyright © {{ new Date().getFullYear() }} Tinycraft.cc All rights reserved
            </div>
        </n-space>
    </n-modal>
</template>

<style lang="scss" scoped>
.about-app-title {
    font-weight: bold;
    font-size: 18px;
    margin: 5px;
}

.about-link {
    cursor: pointer;

    &:hover {
        text-decoration: underline;
    }
}

.about-copyright {
    font-size: 12px;
}
</style>
