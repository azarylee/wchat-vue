<template lang="pug">
    page.chat(margin-bottom)
        nav-bar(slot="nav-bar")
            span.title(slot="title") {{ title }}
            nav-back(slot="left", title="微信",
            @click.native="$router.replace({name: 'message', query: {mode: 'pop'}})")
            span(slot="right") 占位
        ul(slot="main", v-scroll="messages")
            chat-dialog(v-for="(message, index) in messages",
            :date="date(message, index)", :message="message")
        chat-bar(slot="tab-bar", :to="title === '聊天室' ? 'all' : title")
</template>

<script>
import Page from '@/components/common/page'
import NavBar from '@/components/common/nav-bar'
import NavBack from '@/components/common/nav-back'
import ChatDialog from './chat-dialog'
import ChatBar from './chat-bar'

import { mapState } from 'vuex'

export default {
    computed: {
        ...mapState (['sessionList']),
        title () {
            return this.$route.query.title
        },
        index () {
            // 遍历会话列表，看会话是否存在
            let _index = false
            this.sessionList.forEach((session, index) => {
                if (session.title === this.title) {
                    // 存在   记录索引
                    _index = index
                    return
                }
            })
            return _index
        },
        messages () {
            if (this.index !== false) {
                return this.sessionList[this.index].messages
            }
            else {
                return []
            }
        }
    },
    methods: {
        date (message, index) {
            if (index >= 1) {
                // 判断是否显示时间 先将就着用吧
                const time = message.time.substr(3, 2)
                const lastTime = this.messages[index - 1].time.substr(3, 2)
                return time - lastTime > 0
            }
            return false
        }
    },
    directives: {
        // 自动滚动
        scroll: {
            bind (el) {
                setTimeout(() => {
                    el.parentNode.scrollTop = el.scrollHeight
                }, 1)
            },
            update (el) {
                setTimeout(() => {
                    el.parentNode.scrollTop = el.scrollHeight
                }, 1)
            }
        }
    },
    components: {
        Page,
        NavBar,
        NavBack,
        ChatDialog,
        ChatBar
    }
}
</script>

