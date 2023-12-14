<template>
    <div class="wrapper">
        <div v-for="(el, idx) in accordionData" :key="idx" class="accrd">
            <div @click="change(idx)" class="accrd__head"
                 :style="{
                    borderRadius: getRadius.head,
                    paddingTop: getPaddingHead.top,
                    paddingBottom: getPaddingHead.bottom,
                    paddingLeft: getPaddingHead.left,
                    paddingRight: getPaddingHead.right,
                }">
                <h2 class="accrd__title">{{ el.title }}</h2>
            </div>
            <div ref="childrenRefs" class="accrd__content"
                 :style="{
                    marginTop: getMarginTopBody,
                    borderRadius: getRadius.body,
                    paddingLeft: getPaddingBody.left,
                    paddingRight: getPaddingBody.right,
                    paddingTop: children[idx]?.paddingTop ? (borderRadius / 2) + children[idx]?.paddingTop + 'px' : '',
                    paddingBottom: children[idx]?.paddingBottom + 'px',
                }">
                <p class="accrd__desc">{{ el.text }}</p>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import {AccordionData} from "../types/AccordionData.ts";
import {computed, reactive, ref, toRef, watch} from "vue";
interface ChildElement {
    isOpen: Boolean,
    paddingTop: number,
    paddingBottom: number
}
interface SettingsPadding {
    top: number,
    bottom: number,
    left: number,
    right: number,
}

export default {
    props: {
        accordionData: {
            type: Array as () => AccordionData[],
            required: true
        },
        borderRadius: {
            type: Number,
            default: 8
        },
        paddingHead: {
            type: Object as () => SettingsPadding,
            default: () => ({top: 8, right: 8, bottom: 8, left: 8})
        },
        paddingBody: {
            type: Object as () => SettingsPadding,
            default: () => ({top: 8, right: 8, bottom: 8, left: 8})
        },
    },

    setup(props) {
        const toAccordionData = toRef(props, 'accordionData')
        const children = reactive<Array<ChildElement>>([])
        const childrenRefs = ref<HTMLElement[]>([])

        const getRadius = computed(() => {
            return {
                head: props.borderRadius + 'px',
                body: `0 0 ${props.borderRadius + 'px'} ${props.borderRadius + 'px'}`
            }
        })
        const getPaddingHead = computed(() => {
            return {
                top: props.paddingHead.top + 'px',
                bottom: props.paddingHead.bottom + 'px',
                left: props.paddingHead.left + 'px',
                right: props.paddingHead.right + 'px'
            }
        })
        const getPaddingBody = computed(() => {
            return {
                left: props.paddingBody.left + 'px',
                right: props.paddingBody.right + 'px'
            }
        })
        const getMarginTopBody = computed(() => {
            return -(props.borderRadius / 2) + 'px'
        })

        const change = (idx: number) => {
            if(children[idx]) children[idx].isOpen = !children[idx].isOpen

            if(children[idx].isOpen) {
                children[idx].paddingTop = props.paddingBody.top
                children[idx].paddingBottom = props.paddingBody.bottom
                childrenRefs.value[idx].style.maxHeight = childrenRefs.value[idx].scrollHeight + (props.paddingBody.top + props.paddingBody.bottom) + 'px'
            } else {
                children[idx].paddingTop = 0
                children[idx].paddingBottom = 0
                childrenRefs.value[idx].style.maxHeight = '0'
            }
        }

        watch(toAccordionData.value, (val) => {
            children.splice(0, children.length, ...val.map(() => (
                {
                    isOpen: false,
                    paddingTop: 0,
                    paddingBottom: 0,
                }
            )))
        });

        return {
            children,
            getRadius,
            getMarginTopBody,
            getPaddingHead,
            getPaddingBody,
            childrenRefs,
            change,
        }
    }
}
</script>

<style lang="scss" scoped>
.accrd {
    margin-bottom: 16px;
    &:last-child {
        margin-bottom: 0;
    }
    &__head {
        position: relative;
        border: 1px solid lightgray;
        background-color: white;
        cursor: pointer;
        z-index: 3;
    }
    &__title,
    &__desc {
        margin: 0;
    }
    &__content {
        background-color: #f1e7d8;
        max-height: 0;
        overflow: hidden;
        transition: all 0.3s ease-out;
    }
}
</style>
