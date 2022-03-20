<template>
    <v-container>
        <v-row>
            <v-col v-if="top != ''"  cols="12" align="center" justify="center">
                <v-img max-height="250" contain :src="top"></v-img>
            </v-col>
            <v-col cols="12" align="center" justify="center">
                <div >
                    <!-- <video-player class="player"
                    ref="videoPlayer"
                    :options="playerOps"
                    @ready="onPlayerReady"
                    @suspend="suspended($event)"
                    >
                    </video-player> -->
                    <video-player ref="videoPlayer"
                        class="vjs-custom-skin player"
                        :options="playerOps"
                        @ready="onPlayerReady"
                        @suspend="suspended($event)">
                    </video-player>
                </div>
            </v-col>
            <v-col v-if="bottom != ''" cols="12" align="center" justify="center">
                <v-img max-height="250" contain :src="bottom"></v-img>
            </v-col>
        </v-row>
      </v-container>
</template>

<script>

export default {
    name: 'App',
    data: () => ({
        top: '',
        bottom: '',
        playerOps: {
            autoplay: true,
            control: true,
            controlBar: {
                timeDivider: false,
                durationDisplay: false
            },
            html5: {
                vhs: {
                    overrideNative: true
                },
                nativeAudioTracks: false,
                nativeVideoTracks: false,
            }
        },
    }),
    computed: {
        player () {
            return this.$refs.videoPlayer.player
        },
    },

    methods: {
        getData(eid) {
            this.$fire.database.ref(`events/${eid}`).get('once')
            .then((data) => {
                var event = data.val()
                console.log(event);
                this.playVideo(event.liveURL)
            })
        },

        playVideo (url) {
            const video = {
                withCredentials: false,
                type: 'application/x-mpegURL',
                src: url
            }
            
            this.player.reset() // in IE11 (mode IE10) direct usage of src() when <src> is already set, generated errors,
            this.player.src(video)
            this.player.load()
        },
        onPlayerReady () {
            this.player.fill(true)
        },
    },

    mounted () {
        const host = window.location.host;
        const parts = host.split('.');
        this.$fire.database.ref(`streams/${parts[0]}`).get('once')
        .then((data) => {
            var event = data.val()
            this.playVideo(event.url)
            this.top = event.top
            this.bottom = event.bottom
        })
    }
};
</script>

<style scoped>
  .player {
    max-width:950px;
    height: 350px !important;
  }
  .container {
      max-width: 1200px;
  }
</style>
