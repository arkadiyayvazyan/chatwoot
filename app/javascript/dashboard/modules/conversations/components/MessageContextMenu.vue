<template>
  <div class="context-menu">
    <woot-modal
      v-if="isCannedResponseModalOpen && showCannedResponseOption"
      :show.sync="isCannedResponseModalOpen"
      :on-close="hideCannedResponseModal"
    >
      <add-canned-modal
        :response-content="plainTextContent"
        :on-close="hideCannedResponseModal"
      />
    </woot-modal>
    <woot-button
      icon="more-vertical"
      class="button--delete-message"
      color-scheme="secondary"
      variant="link"
      @click="handleContextMenuClick"
    />
    <div
      v-if="isOpen && !isCannedResponseModalOpen"
      v-on-clickaway="handleContextMenuClick"
      class="dropdown-pane dropdown-pane--open"
      :class="`dropdown-pane--${menuPosition}`"
    >
      <woot-dropdown-menu>
        <woot-dropdown-item v-if="showCopy">
          <woot-dropdown-item>
            <woot-button
              variant="clear"
              color-scheme="alert"
              size="small"
              icon="delete"
              @click="handleDelete"
            >
              {{ $t('CONVERSATION.CONTEXT_MENU.DELETE') }}
            </woot-button>
          </woot-dropdown-item>
          <woot-button
            variant="clear"
            size="small"
            icon="clipboard"
            @click="handleCopy"
          >
            {{ $t('CONVERSATION.CONTEXT_MENU.COPY') }}
          </woot-button>
        </woot-dropdown-item>

        <woot-dropdown-item>
          <woot-button
            v-if="showCannedResponseOption"
            variant="clear"
            size="small"
            icon="comment-add"
            @click="showCannedResponseModal"
          >
            {{ $t('CONVERSATION.CONTEXT_MENU.CREATE_A_CANNED_RESPONSE') }}
          </woot-button>
        </woot-dropdown-item>
      </woot-dropdown-menu>
    </div>
  </div>
</template>
<script>
import alertMixin from 'shared/mixins/alertMixin';
import { mixin as clickaway } from 'vue-clickaway';
import messageFormatterMixin from 'shared/mixins/messageFormatterMixin';

import AddCannedModal from 'dashboard/routes/dashboard/settings/canned/AddCanned';
import WootDropdownItem from 'shared/components/ui/dropdown/DropdownItem';
import WootDropdownMenu from 'shared/components/ui/dropdown/DropdownMenu';
import { copyTextToClipboard } from 'shared/helpers/clipboard';

export default {
  components: {
    AddCannedModal,
    WootDropdownMenu,
    WootDropdownItem,
  },
  mixins: [alertMixin, clickaway, messageFormatterMixin],
  props: {
    messageContent: {
      type: String,
      default: '',
    },
    isOpen: {
      type: Boolean,
      default: false,
    },
    showCopy: {
      type: Boolean,
      default: false,
    },
    menuPosition: {
      type: String,
      default: 'left',
    },
    showCannedResponseOption: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return { isCannedResponseModalOpen: false };
  },
  computed: {
    plainTextContent() {
      return this.getPlainText(this.messageContent);
    },
  },
  methods: {
    handleContextMenuClick() {
      this.$emit('toggle', !this.isOpen);
    },
    async handleCopy() {
      await copyTextToClipboard(this.plainTextContent);
      this.showAlert(this.$t('CONTACT_PANEL.COPY_SUCCESSFUL'));
      this.$emit('toggle', false);
    },
    handleDelete() {
      this.$emit('delete');
    },
    hideCannedResponseModal() {
      this.isCannedResponseModalOpen = false;
      this.$emit('toggle', false);
    },
    showCannedResponseModal() {
      this.isCannedResponseModalOpen = true;
    },
  },
};
</script>
<style lang="scss" scoped>
.dropdown-pane {
  bottom: var(--space-medium);
}
.dropdown-pane--left {
  right: var(--space-small);
}
.dropdown-pane--right {
  left: var(--space-small);
}
</style>
