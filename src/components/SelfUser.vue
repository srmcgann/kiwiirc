<template>
    <div class="kiwi-selfuser kiwi-theme-bg">
        <div class="kiwi-close-icon" @click="$emit('close')">
            <i class="fa fa-times" aria-hidden="true"/>
        </div>
        <div class="kiwi-selfuser-mask">
            <span class="kiwi-selfuser-nick">{{ network.nick }}</span>
            <span class="kiwi-selfuser-host">{{ netUser.username }}@{{ netUser.host }}</span>
        </div>
        <div class="kiwi-selfuser-modes">{{ modeString }}</div>
        <div class="kiwi-selfuser-actions">
            <div v-if="error_message">{{ error_message }}</div>
            <input-prompt :label="$t('change_nick')+':'" @submit="changeNick">
                <a class="u-link">{{ $t('change_nick') }}</a>
            </input-prompt>
        </div>
    </div>
</template>

<script>

'kiwi public';

export default {
    props: ['network'],
    data: function data() {
        return {
            error_message: '',
        };
    },
    computed: {
        modeString() {
            let str = '';
            this.network.ircClient.user.modes.forEach((mode) => {
                str += mode;
            });

            // Only show the + if there are modes to show
            if (str) {
                str = '+' + str;
            }

            return str;
        },
        netUser() {
            return this.network.ircClient.user;
        },
    },
    created() {
        this.listen(this.network.ircClient, 'nick in use', (event) => {
            this.error_message = `The nickname '${event.nick}' is already in use!`;
        });
    },
    methods: {
        changeNick(newNick) {
            this.error_message = '';

            let nick = newNick.trim();
            if (!nick.match(/(^[0-9])|(\s)/)) {
                this.network.ircClient.changeNick(newNick);
            }
        },
    },
};
</script>

<style>
.kiwi-selfuser {
    box-sizing: border-box;
    padding: 1em;
}

.kiwi-selfuser-nick {
    display: block;
    font-weight: bold;
    font-size: 1.1em;
}

.kiwi-selfuser-host {
    font-style: italic;
}

.kiwi-selfuser-actions {
    margin-top: 1em;
    padding-top: 1em;
}

.kiwi-selfuser-actions .u-input {
    margin-bottom: 10px;
}
</style>
