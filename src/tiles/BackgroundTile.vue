<template>
    <div v-if="type === 'twitch'" id="twitch" class="background background__twitch"></div>
    <img v-else-if="type === 'image'" :src="src" class="background background__image" />
</template>

<script>
    import { BACKGROUND_TYPE, BACKGROUND_SRC } from '../constants'

    export default {
        data() {
            return {
                type: '',
                src: ''
            }
        },

        created() {
            this.type = BACKGROUND_TYPE
            this.src = BACKGROUND_SRC
        },

        mounted() {
            if (this.type === 'twitch') {
                new Twitch.Embed('twitch', {
                    channel: this.src,
                    layout: 'video',
                    muted: true,
                    controls: false,
                })
            }
        }
    }
</script>

<style lang="scss">
    @import '../variables';

    .background {
        &__image,
        &__twitch iframe {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -100;
        }

        &__image {
            object-fit: cover;
        }

        &__twitch iframe {
            //
        }
    }
</style>