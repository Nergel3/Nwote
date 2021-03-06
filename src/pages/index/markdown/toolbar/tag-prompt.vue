<template>
  <q-dialog :value="active" @hide="close" id="tagPrompt">
    <card>
      <h5 class="q-mb-md q-mt-none">Add a tag to the note.</h5>
      <q-list>
        <div v-if="selectedNote.tags.length">
          <q-item
            v-for="tag in selectedNote.tags"
            :key="tag.fullName"
            clickable
            class="bg-white text-secondary q-pl-md q-pr-sm"
          >
            <q-item-section>
              <span :title="tag.fullName">{{ tag.fullName }}</span>
              <q-btn
                icon="clear"
                @click="delTag(tag)"
                size="xs"
                round
                flat
                class="text-grey-5 float-right"
              />
            </q-item-section>
          </q-item>
        </div>
        <q-item class="bg-white text-secondary q-pl-md q-pr-sm" v-else>
          <q-item-label>No tags on this note</q-item-label>
        </q-item>
      </q-list>
      <div id="tagField">
        <text-field
          placeholder="Enter a tag name..."
          aria-label="tag-field"
          @keypress="addTagEnter"
        />
        <button @click="addTag" class="btn bg-primary text-grey-1">
          <q-icon name="add" />
        </button>
      </div>
    </card>
  </q-dialog>
</template>

<script>
import { mapState } from 'vuex';

import card from 'src/components/card.vue';
import textField from 'src/components/text-field.vue';

export default {
  props: {
    active: Boolean
  },
  components: {
    card,
    textField
  },
  methods: {
    addTagEnter(event) {
      if (event.key === 'Enter') {
        this.addTag();
      }
    },
    addTag() {
      const tagField = document.querySelector('#tagPrompt input[type=text]');
      this.$store.dispatch('addTag', tagField.value);
      tagField.value = '';
    },
    delTag(tag) {
      this.selectedNote.delTag(tag);
      this.$store.dispatch('updateTagList');
      this.$store.dispatch('updateNote');
    },
    close() {
      this.$emit('close');
    }
  },
  computed: mapState({
    selectedNote: state => state.Editor.selectedNote
  }),
  watch: {
    active(newVal) {
      if (newVal) {
        // Async to let the time to the component to be mounted
        setTimeout(() => {
          document.querySelector('#tagPrompt input[type=text]').focus();
        }, 0);
      }
    }
  }
};
</script>

<style lang="stylus" scoped>
#tagField {
  display: flex;

  & button {
    margin-left: 1em;
  }
}

.q-item {
  width 100%
  overflow hidden
  text-overflow: ellipsis;
  white-space: nowrap;

  & span {
    max-width 93%
    overflow hidden
    text-overflow: ellipsis;
    white-space: nowrap;
  }
}

.q-item:first-child {
  border-top-right-radius: 1em;
  border-top-left-radius: 1em;
}

.q-item:last-child {
  border-bottom-right-radius: 1em;
  border-bottom-left-radius: 1em;
  margin-bottom: 1em;
}
</style>
