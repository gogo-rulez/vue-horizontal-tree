<template>
    <div id="app">

        <div
            class="horizontal_tree"
            v-if="pageReady">

            <div class="horizontal_tree__stage">
                <draggable-element
                    v-for="block in blocks.slice(0,1)"
                    :key="block.id"
                    :block-id="block.id"
                    :block-label="block.label"
                    :blocks="blocks"
                    :drag-start-index.sync="dragStartIndex"
                    :drag-end-index.sync="dragEndIndex"
                    :drop-index.sync="dropIndex" />
            </div>


            <div class="horizontal_tree__block_pool">

                <add-draggable-element :blocks.sync="blocks" />

                <div
                    class="horizontal_tree__blocks"
                    @drop.capture="drop_handler($event, 'pool')"
                    @dragover="dragover_handler($event)"
                    id="pool">

                    <draggable-element
                        v-for="block in blocks.slice(1)"
                        :key="block.id"
                        :block-id="block.id"
                        :block-label="block.label"
                        :blocks="blocks"
                        :drag-start-index.sync="dragStartIndex"
                        :drag-end-index.sync="dragEndIndex"
                        :drop-index.sync="dropIndex" />

                </div>


            </div>

        </div>



    </div>
</template>

<script>

import DraggableElement from '@/components/DraggableElement';
import AddDraggableElement from '@/components/AddDraggableElement';

export default {
    name: 'App',

    data () {
        return {
            pageReady: false,
            dragStartIndex: null,
            dragEndIndex: null,
            dropIndex: null,
            blocks: [
                {
                    id: 0,
                    label: 'item 0',
                    parentId: null
                },
                {
                    id: 1,
                    label: 'item 1',
                    parentId: null
                },
                {
                    id: 2,
                    label: 'item 2',
                    parentId: null
                },
                {
                    id: 3,
                    label: 'item 3',
                    parentId: null
                },
                {
                    id: 4,
                    label: 'item 4',
                    parentId: null
                },
                {
                    id: 5,
                    label: 'item 5',
                    parentId: null
                },
                {
                    id: 6,
                    label: 'item 6',
                    parentId: null
                },
                {
                    id: 7,
                    label: 'item 7',
                    parentId: null
                },
                {
                    id: 8,
                    label: 'item 8',
                    parentId: null
                },
            ],
        };
    },

    components: {
        DraggableElement,
        AddDraggableElement
    },

    mounted () {
        this.pageReady = true;
    },

    methods: {
        dragover_handler (ev) {
            console.log('dragover_handler');
            ev.preventDefault();
            ev.dataTransfer.dropEffect = 'move';
        },

        drop_handler (ev) {
            ev.preventDefault();
            this.dropIndex = null;
            const dropZoneTarget = document.querySelector('#pool');
            const draggedItem = document.querySelector(`#block-${this.dragStartIndex}`);
            dropZoneTarget.appendChild(draggedItem);
        },
    }
};
</script>

<style lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/minireset.css/0.0.2/minireset.min.css");

.horizontal_tree {
    position: relative;
    flex-flow: row wrap;
    width: 100%;
    height: 100vh;
    min-height: 800px;
    margin: auto;

    &__stage {
        display: flex;
        justify-content: center;
    }

    &__block_pool {
        position: absolute;
        right: 30px;
        bottom: 30px;
        left: 30px;
        background-color: rgba(#ccc, 0.1);
        border: 1px solid #aeaeae;
    }

    &__new_block_wrap {
        display: inline-flex;
        flex-direction: column;
        width: 200px;
        padding: 20px;

        input {
            margin: 15px 0 20px;
        }
    }
}
</style>
