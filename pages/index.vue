<template>
<v-layout column justify-center align-center class='pt-10'>
    <v-flex class="chat-room d-flex">
        <!--sidebar-->
        <div class="sidebar">
            <div class="profile-data d-flex align-center pa-lg-5 pa-md-5 pa-sm-5 pa-2">
                <v-avatar>
                    <img src="https://placekitten.com/300/300" alt="cat" />
                </v-avatar>
                <p class='ml-5 mb-0 text-white'>Mike Ross</p>
            </div>
            <div class="search-area">
                <font-awesome-icon class='' :icon="['fas','search']" />
                <input type="text" />
            </div>
            <div class="list-friends-group">
                <v-list three-line>
                    <template v-for="item in items">

                        <v-list-item :key="item.index">
                            <v-list-item-avatar>
                                <v-img :src="item.avatar"></v-img>
                                <v-badge color="green"></v-badge>
                            </v-list-item-avatar>

                            <v-list-item-content>
                                <v-row>
                                    <v-col cols='8'>
                                        <v-list-item-title v-html="item.name"></v-list-item-title>
                                        <v-list-item-subtitle v-html="item.subtitle"></v-list-item-subtitle>
                                    </v-col>
                                    <v-col>
                                        <div class='state'>
                                            <div class="timer mb-3 text-right">AM 09:00</div>
                                            <div class="read text-center">
                                                <v-badge color="green" content="105"></v-badge>
                                            </div>
                                        </div>
                                    </v-col>
                                </v-row>
                            </v-list-item-content>
                        </v-list-item>

                    </template>
                </v-list>
            </div>
        </div>
        <!--sidebar-->
        <!--right-column-->
        <div class="chat-room-content bg-white">
            <div class="profile-share pa-4 d-flex align-center justify-space-between">
                <div class="profile-data d-flex align-center">
                    <v-avatar>
                        <img src="https://placekitten.com/300/300" alt="cat" />
                    </v-avatar>
                    <p class='ml-5 mb-0'>Mike Ross</p>
                </div>

                <div class="share-area">
                    <font-awesome-icon class='mr-3' :icon="['fab','facebook-f']" />
                    <font-awesome-icon class='mr-3' :icon="['fab','twitter']" />
                    <font-awesome-icon :icon="['fab','instagram-square']" />
                </div>

            </div>

            <div class="message-area pa-5 bg-CatskillWhite">
                <ul>
                    <li class='d-flex' v-for='chat in chatMessageData' :key='chat.index' :class="[chat.from == 'other'?'send':'replies']">
                        <v-avatar v-if="chat.from=='other'" size="36" class='mr-3'>
                            <img :src="chat.image" alt="cat" />
                        </v-avatar>
                        <div class="message-box">
                            <div class="message" v-html='chat.message'></div>
                            <div class="timer">{{chat.timer}}</div>
                        </div>
                        <v-avatar v-if="chat.from =='self'" size="36" class='ml-3'>
                            <img :src="chat.image" alt="cat" />
                        </v-avatar>
                    </li>
                </ul>
            </div>

            <div class="message-input-area d-flex justify-center align-items-center">
                <emoji :emoji-data="emojiData" :emoji-category="emojiCategory" />
                <div class="message-input">
                    <input type="text" v-model="chatMessageInput">
                </div>
                <div class="message-send" @click='sendMessage()'>
                    send
                </div>
            </div>
        </div>

    </v-flex>
</v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'
import {
    FontAwesomeIcon
} from '@fortawesome/vue-fontawesome'
import {
    gsap
} from 'gsap/dist/gsap'
import {
    ScrollToPlugin
} from 'gsap/dist/ScrollToPlugin'
import {
    emojiCategory,
    emojiData
} from '../mixins/emoji'
import emoji from '~/components/emoji'
gsap.registerPlugin(ScrollToPlugin)

import * as signalR from '@aspnet/signalr'

export default {
    components: {
        Logo,
        VuetifyLogo,
        FontAwesomeIcon,
        emoji
    },
    data: () => ({
        items: [
            // { header: 'Today' },
            {
                avatar: 'https://cdn.vuetifyjs.com/images/lists/1.jpg',
                name: 'mike',
                title: 'Brunch this weekend?',
                subtitle: "<span class='text--primary'>Ali Connors</span> &mdash; I'll be in your neighborhood doing errands this weekend. Do you want to hang out?",
            },
            // { divider: true, inset: true },
            {
                avatar: 'https://cdn.vuetifyjs.com/images/lists/2.jpg',
                name: 'dog',
                title: 'Summer BBQ <span class="grey--text text--lighten-1">4</span>',
                subtitle: "<span class='text--primary'>to Alex, Scott, Jennifer</span> &mdash; Wish I could come, but I'm out of town this weekend.",
            },
            // { divider: true, inset: true },
            {
                avatar: 'https://cdn.vuetifyjs.com/images/lists/3.jpg',
                name: 'cat',
                title: 'Oui oui',
                subtitle: "<span class='text--primary'>Sandra Adams</span> &mdash; Do you have Paris recommendations? Have you ever been?",
            },
            // { divider: true, inset: true },
            {
                avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
                name: 'cat',
                title: 'Birthday gift',
                subtitle: "<span class='text--primary'>Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?",
            },
            // { divider: true, inset: true },
            {
                avatar: 'https://cdn.vuetifyjs.com/images/lists/5.jpg',
                name: 'cat',
                title: 'Recipe to try',
                subtitle: "<span class='text--primary'>Britta Holt</span> &mdash; We should eat this: Grate, Squash, Corn, and tomatillo Tacos.",
            },
            // { divider: true, inset: true },
            {
                avatar: 'https://cdn.vuetifyjs.com/images/lists/5.jpg',
                name: 'cat',
                title: 'Recipe to try',
                subtitle: "<span class='text--primary'>Britta Holt</span> &mdash; We should eat this: Grate, Squash, Corn, and tomatillo Tacos.",
            },
            // { divider: true, inset: true },
            {
                avatar: 'https://cdn.vuetifyjs.com/images/lists/5.jpg',
                name: 'cat',
                title: 'Recipe to try',
                subtitle: "<span class='text--primary'>Britta Holt</span> &mdash; We should eat this: Grate, Squash, Corn, and tomatillo Tacos.",
            },
        ],
        emojiData: emojiData,
        emojiCategory: emojiCategory,
        isEmojiPanels: false,
        lockMessageArray: ["ABC", "CCC"],
        chatMessageData: [],
        chatMessageInput: '',
        singlR: null,
    }),
    mounted() {
        let _this = this

        // 建立SignalR連接
        let connection = new signalR.HubConnectionBuilder().withUrl("https://localhost:44327/chatHub").build();
        _this.singlR = connection

        // 連接事件
        _this.singlR.start().catch(function (err) {
            return console.error(err.toString());
        });

        // 傳送訊息事件
        _this.singlR.on("ReceiveMessage", function (user, message) {
            if (user == 'test') {
                return false
            }
            _this.getMessage(user, message)
        });

        // 接收
        this.$nuxt.$on("selectEmoji", emoji => {
            if (emoji.category == "stickers01") {
                this.postSticker(emoji)
            } else {
                this.selectEmoji(emoji)
            }

        })
    },
    methods: {
        getMessage(user, message) {
            let _this = this
            var data = {
                image: 'https://placekitten.com/300/300',
                name: user,
                message: message,
                timer: '2017-07-09 00:08:02',
                from: 'other'
            }
            _this.chatMessageData.push(data);
            this.scrollToEnd()
        },
        sendMessage() {
            this.liveIndex = this.liveIndex + 1
            let _this = this

            if (_this.chatMessageInput != '') {
                let lockMessage = false
                let replace = function (string, target, pos) {
                    let t = "";
                    if (pos == 'lockMessage') {
                        for (var i = 0; i < target.length; i++) {
                            t += "*";
                        }
                        return string.replace(new RegExp(target, 'g'), t);
                    } else {

                        for (var i = 0; i < target.length; i++) {
                            console.log(target[i].data)
                            t = '<img style="width:30px" src="' + target[i].data + '"/>';
                        }
                        return string.replace(new RegExp(target[i - 1].text, 'g'), t);
                    }
                };

                let clean = function (string) {
                    for (var i = 0; i < _this.lockMessageArray.length; i++) {
                        if (string.indexOf(_this.lockMessageArray[i]) > -1) {
                            string = replace(string, _this.lockMessageArray[i], 'lockMessage');
                        }
                    }
                    return string;
                };

                let emotions = function (string) {
                    console.log(string)
                    // 
                    /// 將字串表情符號找到的時候拆成陣列
                    // 做比對
                    // 傳進replace
                    let arr = []
                    // string = string.split("/")

                    for (var i = 0; i < _this.emojiData.length; i++) { // 裡面撈取資料 => 兩筆應該撈取兩次
                        console.log(i)
                        if (string.indexOf(_this.emojiData[i].text) > -1) { // 搜尋到一個舊船全部
                            arr.push(_this.emojiData[i])
                            console.log(arr)
                            string = replace(string, arr, 'emotions')
                        }
                    }

                    return string
                }

                // _this.lockMessageArray.forEach((word) => {
                //     _this.chatMessageInput = _this.chatMessageInput.replace(new RegExp('\\b' + word + '\\b', 'g'),
                //         word => lockMessage = true
                //     );
                // })
                // if (lockMessage == true) {
                //     return false
                // }

                _this.chatMessageInput = clean(_this.chatMessageInput)
                _this.chatMessageInput = emotions(_this.chatMessageInput)

                var data = {
                    image: 'https://placekitten.com/300/300',
                    name: 'authorName',
                    message: this.chatMessageInput,
                    timer: '2017-07-09 00:08:02',
                    from: 'self'
                }

                this.chatMessageData.push(data);
                let user = 'test'
                let message = this.chatMessageInput
                this.singlR.invoke("SendMessage", user, message).catch(function (err) {
                    return console.error(err.toString());
                });
                this.chatMessageInput = ''
                this.scrollToEnd()
            }
        },
        selectEmoji(emoji) {
            console.log(emoji)
            var data = {
                image: 'https://placekitten.com/300/300',
                name: 'authorName',
                message: emoji.text,
                timer: 'AM 05:55',
                from: 'self'
            }

            // let splitData = data.message.split('/')[1]
            // console.log(splitData)
            // splitData = splitData.substr(0, 5)
            let arrData = []
            arrData.push(data.message)
            // console.log(splitData)

            // if (splitData == undefined) {
            //     return false
            // }

            let result = emojiData.filter(function (element, index, arr) {
                for (var i = 0; i < arrData.length; i++) {
                    if (element.text == arrData[i]) {
                        return element
                    }
                }
            });

            let changeEmoji = ''
            for (var i = 0; i < arrData.length; i++) {
                for (var j = 0; j < result.length; j++) {
                    if (result[j].text == arrData[i]) {
                        // changeEmoji += '<img src=' + result[j].data + '>'
                        changeEmoji += result[j].text
                    }
                }
            }
            console.log(changeEmoji)

            this.chatMessageInput += changeEmoji
            // data.message = changeEmoji
            // this.chatMessageInput = data.message
            // this.chatMessageData.push(data);
        },
        postSticker(item) {
            console.log('sticker')
            console.log(item.text)
            var data = {
                image: 'https://placekitten.com/300/300',
                name: 'authorName',
                message: item.text,
                timer: 'AM 05:55',
                from: 'self'
            }

            let splitData = data.message.split('s')
            if (splitData == undefined) {
                return false
            }

            let result = emojiData.filter(function (element, index, arr) {
                for (var i = 0; i < splitData.length; i++) {
                    if (element.text == 's' + splitData[i]) {
                        return element
                    }
                }
            });

            let changeEmoji = ''
            for (var i = 0; i < splitData.length; i++) {
                for (var j = 0; j < result.length; j++) {
                    if (result[j].text == 's' + splitData[i]) {
                        changeEmoji += '<img src=' + result[j].data + '>'
                    }
                }
            }

            data.message = changeEmoji
            this.chatMessageData.push(data);
            this.scrollToEnd()
        },
        scrollToEnd() {
            let objDiv = document.getElementsByClassName('message-area')[0]
            gsap.to(objDiv, .3, {
                scrollTo: {
                    y: objDiv.scrollHeight
                },
                ease: 'easing'
            });
        },
    }
}
</script>
