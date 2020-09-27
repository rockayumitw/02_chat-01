<template>
<div class="message-emoji">
    <font-awesome-icon :icon="['far','smile']" class='mt-1' @click='openPanel()' />
    <div class="emoji-panel" v-show='isEmojiPanels'>
        <ul>
            <div class="d-flex">
                <div @click="changePanels">{{panels.name}}</div>
            </div>
            <li v-for="emoji in emojiData" :key="emoji.text" @click='selectEmoji(emoji)'>
                <img class='img-fluid' :src='emoji.data' />
            </li>
        </ul>
    </div>
</div>
</template>

<script>
export default {
    props: ['emojiData', 'emojiCategory'],
    data() {
        return {
            isEmojiPanels: false,
            panelsName: 'emotions',
            isPanels: true
        }
    },
    computed: {
        panels() {
            let panelsName = this.panelsName
            return this.emojiCategory.filter(select => select.name == panelsName)
        }
    },
    mounted() {
        console.log(this.emojiCategory)
    },
    methods: {
        changePanels() {
            // this.isPanels ? this.panelsName = 'emotions' : this.panelsName = 'stickers'
        },
        openPanel() {
            console.log('click')
            this.isEmojiPanels = !this.isEmojiPanels
        },
        selectEmoji(emoji) {
            console.log(emoji)
            this.$nuxt.$emit("selectEmoji", emoji)
            console.log('click')
        }
    }
}
</script>
