<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="builder-with-slash" :style="customStyle">
        <!-- wwManager:start -->
        <wwSectionEditMenu v-bind:sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
        <!-- wwManager:end -->

        <!-- This is the background of the section -->
        <wwObject class="background" v-bind:ww-object="section.data.background" ww-category="background"></wwObject>

        <div class="content-container">
            <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.content" class="list" @ww-add="add(section.data.content, $event)" @ww-remove="remove(section.data.content, $event)">
                <wwObject tag="div" v-for="object in section.data.content" :key="object.uniqueId" :ww-object="object"></wwObject>
            </wwLayoutColumn>
            <!-- slash column -->
            <div ref="slashContainer" class="slash-container">
                <div class="left"></div>
                <div class="right"></div>
            </div>
        </div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
/* wwManager:start */
import ResizeObserver from 'resize-observer-polyfill';
wwLib.wwPopups.addStory('TWO_COLUMN_SLASH_CONFIG', {
    title: {
        en: 'Section config',
        fr: 'Configuration de la section'
    },
    type: 'wwPopupForm',
    storyData: {
        fields: [
            {
                label: {
                    en: 'Slash width (in px):',
                    fr: 'Largeur de l\'oblique (en px) :'
                },
                type: 'text',
                key: 'slashWidth',
                valueData: 'slashWidth'
            },
            {
                label: {
                    en: 'Slash screen starting point (in %):',
                    fr: 'Position de départ de l\'oblique (en %) :'
                },
                type: 'text',
                key: 'slashPosition',
                valueData: 'slashPosition',
            },
            {
                label: {
                    en: 'Slash left color:',
                    fr: 'couleur gauche de l\'oblique :'
                },
                type: 'color',
                key: 'slashLeftcolor',
                valueData: 'slashLeftcolor',
            },
            {
                label: {
                    en: 'Slash right color:',
                    fr: 'couleur droite de l\'oblique :'
                },
                type: 'color',
                key: 'slashRightColor',
                valueData: 'slashRightColor',
            }
        ]
    },
    buttons: {
        NEXT: {
            text: {
                en: 'Ok',
                fr: 'Ok'
            },
            next: false
        }
    }
})
/* wwManager:end */
export default {
    name: "__COMPONENT_NAME__",
    props: {
        sectionCtrl: Object
    },
    data() {
        return {
            sectionHeight: 0,
            slashWidth: '50',
            slashLeftcolor: '#000000',
            slashRightColor: '#ffffff',
            slashPosition: '50'
        }
    },
    computed: {
        section() {
            return this.sectionCtrl.get();
        },
        customStyle() {
            return {
                '--sectionHeight': `${this.sectionHeight}px`,
                '--slashWidth': `${this.slashWidth}px`,
                '--slashLeftcolor': this.slashLeftcolor,
                '--slashRightColor': this.slashRightColor,
                '--slashPosition': `${this.slashPosition}%`
            }
        }

    },
    created() {
        this.init()
    },
    mounted() {
        this.updateVars()
        /* wwManager:start */
        const myObserver = new ResizeObserver(entries => {
            for (let entry of entries) {
                this.updateVars()
            }

        });
        myObserver.observe(this.$refs.slashContainer);
        /* wwManager:end */
    },
    methods: {
        init() {
            // Initialize section data
            let needUpdate = false;

            this.section.data = this.section.data || {};

            if (!this.section.data.background) {
                this.section.data.background = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }

            if (!this.section.data.leftSlash) {
                this.section.data.leftSlash = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }

            if (!this.section.data.rightSlash) {
                this.section.data.rightSlash = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }

            if (!this.section.data.content) {
                this.section.data.content = [];
                needUpdate = true;
            }

            this.slashWidth = this.section.data.slashWidth || this.slashWidth
            this.slashLeftcolor = this.section.data.slashLeftcolor || this.slashLeftcolor
            this.slashRightColor = this.section.data.slashRightColor || this.slashRightColor
            this.slashPosition = this.section.data.slashPosition || this.slashPosition

            if (needUpdate) {
                this.sectionCtrl.update(this.section);
            }
        },
        updateVars() {
            if (this.$refs.slashContainer) {
                this.sectionHeight = this.$refs.slashContainer.clientHeight;
            }
        },

        // --------- EDITOR FUNCTIONS ---------
        /* wwManager:start */
        add(list, options) {
            try {
                list.splice(options.index, 0, options.wwObject);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        remove(list, options) {
            try {
                list.splice(options.index, 1);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        async openOptions() {
            try {
                let options = {
                    firstPage: 'TWO_COLUMN_SLASH_CONFIG',
                    data: {
                        slashWidth: this.slashWidth,
                        slashLeftcolor: this.slashLeftcolor,
                        slashRightColor: this.slashRightColor,
                        slashPosition: this.slashPosition
                    },
                }
                const result = await wwLib.wwPopups.open(options)
                let needUpdate = false;
                if (typeof (result) == 'undefined') return;
                if (typeof (result.slashWidth) != 'undefined') {
                    this.section.data.slashWidth = result.slashWidth
                    this.slashWidth = result.slashWidth
                    needUpdate = true;
                }
                if (typeof (result.slashLeftcolor) != 'undefined') {
                    this.section.data.slashLeftcolor = result.slashLeftcolor
                    this.slashLeftcolor = result.slashLeftcolor
                    needUpdate = true;
                }
                if (typeof (result.slashRightColor) != 'undefined') {
                    this.section.data.slashRightColor = result.slashRightColor
                    this.slashRightColor = result.slashRightColor
                    needUpdate = true;
                }
                if (typeof (result.slashPosition) != 'undefined') {
                    this.section.data.slashPosition = result.slashPosition
                    this.slashPosition = result.slashPosition
                    needUpdate = true;
                }
                if (typeof (result.leftColumnWidth) != 'undefined') {
                    this.section.data.leftColumnWidth = result.leftColumnWidth
                    this.leftColumnWidth = result.leftColumnWidth
                    needUpdate = true;
                }
                if (typeof (result.rightColumnWidth) != 'undefined') {
                    this.section.data.rightColumnWidth = result.rightColumnWidth
                    this.rightColumnWidth = result.rightColumnWidth
                    needUpdate = true;
                }
                if (needUpdate) {
                    this.sectionCtrl.update(this.section);
                    this.$forceUpdate();
                }

            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        }
        /* wwManager:end */
    }

};
</script>

<!-- This is your CSS -->
<style lang="scss" scoped>
.builder-with-slash {
    .content-container {
        position: relative;
        height: 100%;
        width: 100%;
        .slash-container {
            display: none;
            position: absolute;
            top: 0;
            height: 100%;
            left: var(--slashPosition);
            z-index: 2;
            @media (min-width: 992px) {
                display: block;
            }
            .left {
                position: absolute;
                top: 0;
                right: 0;
                width: 0;
                height: 0;
                transform: translateX(100%);

                border-bottom: var(--sectionHeight) solid transparent;
                border-left: var(--slashWidth) solid var(--slashLeftcolor);
            }
            .right {
                position: absolute;
                top: 0;
                left: 0;
                width: 0;
                height: 0;
                transform: translateX(0);

                border-top: var(--sectionHeight) solid transparent;
                border-right: var(--slashWidth) solid var(--slashRightColor);
            }
        }
    }
}

.background {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
}
</style>
