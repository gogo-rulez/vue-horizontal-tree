<template>
    <div class="horizontal_tree__new_block_wrap">
        <label for="new_block_label">New block label</label>
        <input
            v-model="newBlockLabel"
            type="text"
            id="new_block_label"
            name="new_block_label">
        <button
            type="button"
            :disabled="!newBlockLabel"
            @click="createNewBlock()">
            Create new block
        </button>
    </div>
</template>

<script>
export default {
    name: 'AddDraggableElement',

    props: {
        blocks: {
            required: true,
            type: Array
        }
    },

    data () {
        return {
            newBlockLabel: ''
        };
    },

    methods: {
        createNewBlock () {
            const currentBlockLength = this.blocks.length;
            const newBlockObject = {
                id: currentBlockLength,
                label: this.newBlockLabel,
                parentId: null
            };

            const newBlocks = [...this.blocks, newBlockObject];

            this.$emit('update:blocks', newBlocks);
        }
    }
};
</script>
