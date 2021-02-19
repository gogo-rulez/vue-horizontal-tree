<template>
    <div
        class="draggable_element__wrap js_draggable"
        draggable="true"
        @dragstart="dragstart_handler($event, blockId);"
        @dragend="dragend_handler($event, blockId)"
        :id="`block-${blockId}`">
        <div
            class="draggable_element"
        >
            {{ blockLabel }}
        </div>
        <div
            class="draggable_element__drop_zone">
            <div
                class="draggable_element__drop_zone_trigger"
                @drop.capture="drop_handler($event, blockId)"
                @dragover="dragover_handler($event)"
                @click="childrenCollapsed = !childrenCollapsed"
                :id="`target-${blockId}`">
                <div
                    v-if="childrenCollapsed"
                    class="expand" />
                <div
                    v-else
                    class="collapse"/>
            </div>
            <div
                :class="{'is-hidden': childrenCollapsed}"
                class="draggable_element__children"
            ></div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'DraggableElement',

    data () {
        return {
            childrenCollapsed: true
        };
    },

    props: {
        blockId: {
            required: true,
            type: Number
        },
        blockLabel: {
            required: true,
            type: String
        },
        blocks: {
            required: true,
            type: Array
        },
        dragStartIndex: {
            type: [Number, null]
        },
        dragEndIndex: {
            type: [Number, null]
        },
        dropIndex: {
            type: [Number, null]
        }
    },

    methods: {
        dragstart_handler (ev, blockIndex) {
            ev.stopPropagation();
            this.$emit('update:dragStartIndex', blockIndex);
            ev.dataTransfer.dropEffect = 'move';
        },

        dragend_handler (ev, blockIndex) {
            this.$emit('update:dragEndIndex', blockIndex);
            if (this.dragEndIndex !== this.dragStartIndex) return;
            // Remove all of the drag data
            ev.dataTransfer.clearData();
        },

        dragover_handler (ev) {
            this.childrenCollapsed = false;
            ev.preventDefault();
            ev.dataTransfer.dropEffect = 'move';
        },

        async drop_handler (ev, blockIndex) {

            ev.preventDefault();

            const updateDropIndex = () => new Promise (resolve => {
                this.$emit('update:dropIndex', blockIndex);
                setTimeout(() => {
                    resolve(true);
                }, 0);
            });

            await updateDropIndex();

            const childrenLength = this.blocks.filter(x => x.parentId === this.dropIndex).length;

            // every block can have at most 2 children
            if (childrenLength === 2) return;

            this.blocks[this.dragStartIndex].parentId = this.dropIndex;

            // see if the new drop zone has an ancestor with and id equal to the id of the currently dropped block
            // if it does, traverse up the dom and rearrange it
            const dropZoneBlock = document.querySelector(`#block-${this.dropIndex}`);
            const dropZoneBlockAncestor = dropZoneBlock.closest(`#block-${this.dragStartIndex}`);
            if (dropZoneBlockAncestor) {
                const ancestorParent = dropZoneBlockAncestor.parentElement;
                ancestorParent.insertBefore(dropZoneBlock, dropZoneBlockAncestor);
            }

            // Get the id of the target and add the moved element to the target's DOM
            const dropZoneTarget = document.querySelector(`#target-${this.dropIndex}`);
            const draggedItem = document.querySelector(`#block-${this.dragStartIndex}`);
            dropZoneTarget.nextSibling.appendChild(draggedItem);

            // update the parentId for the dropZone element
            const closestParentBlock = dropZoneBlock.parentElement.closest('.js_draggable');
            if (closestParentBlock) {
                this.blocks[this.dropIndex].parentId = closestParentBlock.id.split('block-')[1];
                return;
            }

            this.blocks[this.dropIndex].parentId = null;
        },
    }
};
</script>

<style lang="scss" scoped>
.draggable_element {
    position: relative;
    z-index: 10;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    flex: 1 0 auto;
    height: 50px;
    padding: 0 20px;
    border: 1px solid #aeaeae;
    border-radius: 4px;
    background-color: #fff;

    &__wrap {
        position: relative;
        display: inline-flex;
        align-items: center;
        justify-content: flex-start;
        min-height: 140px;
        margin: 20px;
    }

    &__drop_zone {
        position: relative;
        display: flex;
        align-items: center;
        box-sizing: border-box;
        min-width: 52px;
        min-height: 50px;
        padding: 0 0 0 52px;
        flex: 1 0 auto;

        &:before {
            content: " ";
            position: absolute;
            top: 0;
            bottom: 0;
            left: 0;
            height: 1px;
            width: 30px;
            margin: auto;
            background-color: #aeaeae;
        }

        &:after {
            content: " ";
        }
    }

    &__children {
        position: relative;
        z-index: 2;
        box-sizing: border-box;
        display: flex;
        flex-direction: column;
        min-height: 50px;
        min-width: 52px;
        padding: 0 0 0 52px;
        margin: 0 0 0 -52px;

        &.is-hidden {
            display: none;
        }

        .draggable_element__wrap {
            margin: 0;

            &:only-child {
                position: relative;
                margin: 0 0 0 10px;

                &::before {
                    content: " ";
                    position: absolute;
                    top: 0;
                    bottom: 0;
                    left: -10px;
                    width: 10px;
                    height: 1px;
                    background-color: #aeaeae;
                    margin: auto;
                }
            }

            &:not(:only-child):nth-child(1) {
                position: relative;

                &::before {
                    content: " ";
                    position: absolute;
                    top: 0;
                    bottom: 0;
                    left: -10px;
                    width: 10px;
                    height: 1px;
                    margin: auto;
                    background-color: #aeaeae;
                }

                &::after {
                    content: " ";
                    position: absolute;
                    top: 50%;
                    left: -10px;
                    width: 1px;
                    height: 50%;
                    background-color: #aeaeae;
                }
            }

            &:nth-child(2) {
                position: relative;

                &::before {
                    content: " ";
                    position: absolute;
                    top: 0;
                    bottom: 0;
                    left: -10px;
                    width: 10px;
                    height: 1px;
                    margin: auto;
                    background-color: #aeaeae;
                }

                &::after {
                    content: " ";
                    position: absolute;
                    bottom: 50%;
                    left: -10px;
                    width: 1px;
                    height: 50%;
                    background-color: #aeaeae;
                }
            }
        }
    }



    &__drop_zone_trigger {
        position: absolute;
        top: 0;
        bottom: 0;
        left: 30px;
        z-index: 3;
        display: flex;
        align-items: center;
        justify-content: center;
        width: 20px;
        height: 20px;
        border: 1px solid #aeaeae;
        border-radius: 50%;
        margin: auto;
        background-color: #fff;
        font-size: 20px;
        line-height: 20px;

        div {
            position: relative;
            display: flex;
            align-items: center;
            justify-content: center;
            width: 20px;
            height: 20px;

            &.expand {
                &::after {
                    content: " ";
                    position: absolute;
                    top: 0;
                    right: 0;
                    bottom: 0;
                    left: 0;
                    width: 10px;
                    height: 2px;
                    margin: auto;
                    background-color: #aeaeae;
                }

                &::before {
                    content: " ";
                    position: absolute;
                    top: 0;
                    right: 0;
                    bottom: 0;
                    left: 0;
                    width: 2px;
                    height: 10px;
                    margin: auto;
                    background-color: #aeaeae;
                }
            }

            &.collapse {
                &::after {
                    content: " ";
                    position: absolute;
                    top: 0;
                    right: 0;
                    bottom: 0;
                    left: 0;
                    width: 10px;
                    height: 1px;
                    margin: auto;
                    background-color: #aeaeae;
                }
            }
        }
    }

    &__droppable_zone {
        position: relative;
        box-sizing: border-box;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        flex: 1 0 auto;
        height: 50px;
        width: 100%;
        padding: 0 20px;
        margin: 10px 0;
        background-color: #aeaeae;
        border-radius: 4px;
        opacity: 0.5;

        &:before {
            content: " ";
            position: absolute;
            right: 100%;
            width: 10px;
            height: 1px;
            background-color: #aeaeae;
        }
    }
}
</style>
