<template>
  <div ref="commentRef" id="comment-dom" :class="['comment-content', 'artalk', { fill }]" />
</template>

<script setup>
import initComments from "@/utils/initComments";

const route = useRoute();
const props = defineProps({
  fill: {
    type: [Boolean, String],
    default: false,
  },
});

const { theme } = useData();
const { comment } = theme.value;

const artalk = ref(null);
const commentRef = ref(null);

const initArtalk = async () => {
  try {
    await nextTick();
    const Artalk = await initComments(theme.value);
    artalk.value = Artalk.init({
      el: commentRef.value || "#comment-dom",
      locale: "auto",
      pageKey: route.path,
      site: comment.artalk.site,
      server: comment.artalk.server,
    });
    // Event
    artalk.value?.on("created", () => {
      if (props.fill) fillComments(props.fill);
    });
    artalk.value?.on("list-loaded", () => {
      console.log("Comments Loaded");
    });
    artalk.value?.on("comment-updated", () => {
      console.log("Comments Updated");
    });
    if (typeof $comment === "undefined" && typeof window !== "undefined") {
      window.$comment = artalk.value;
    }
    return artalk.value;
  } catch (error) {
    console.error("Initializing Comment Failed: ", error);
  }
};

const fillComments = (data) => {
  console.log("Filling Comment: ", data);
  const commentDom = document.querySelector("#comment-dom.fill");
  if (!commentDom) return false;
  const commentInput = commentDom.querySelector("textarea");
  commentInput.value = data + "\n\n";
  commentInput.focus();
};

// Watch Page Change
watch(
  () => route.path,
  (val) => {
    nextTick().then(() => {
      artalk.value?.update({ pageKey: val });
      artalk.value?.reload();
    });
  },
);

onMounted(() => {
  initArtalk();
});

onUnmounted(() => {
  artalk.value?.destroy();
});
</script>

<style lang="scss">
#comment-dom,
.atk-layer-wrap,
.comment-content {
  --at-color-font: var(--main-font-color) !important;
  --at-color-deep: var(--main-font-second-color) !important;
  --at-color-grey: var(--main-font-second-color) !important;
  --at-color-meta: var(--main-font-second-color) !important;
  --at-color-border: var(--main-card-border) !important;
  --at-color-main: var(--main-color) !important;
  --at-color-light: var(--main-color) !important;
  --at-color-bg: var(--main-card-background) !important;
  --at-color-bg-grey: var(--main-card-border) !important;
  --at-color-bg-grey-transl: var(--main-card-border) !important;
  --at-color-bg-transl: var(--main-card-second-background) !important;
  --at-color-gradient: linear-gradient(180deg, transparent, var(--main-card-background)) !important;
}
.atk-layer-wrap {
  .atk-layer-mask {
    background: var(--main-mask-background) !important;
  }
}
</style>

<style lang="scss" scoped>
#comment-dom {
  :deep(.atk-main-editor) {
    .atk-bottom {
      padding: 0 0 0 8px;
      height: 40px;
      .atk-send-btn {
        height: 40px;
      }
    }
    .atk-user-btn,
    .atk-plug-btn {
      transition: background 0.3s;
    }
    .atk-plug-panel-wrap {
      .atk-grp {
        &[data-grp-name="小黄脸"] {
          .atk-item {
            font-size: 2rem;
            transition: background 0.3s;
          }
        }
      }
      .atk-grp-switcher {
        span {
          transition: background 0.3s;
        }
      }
    }
  }
  :deep(.atk-list) {
    .atk-list-header {
      .atk-dropdown {
        .atk-dropdown-item {
          margin: 0;
          padding: 0;
          margin-top: 8px;
          line-height: normal;
          text-align: center;
          letter-spacing: normal;
          &:first-child {
            margin-top: 0;
          }
          &::before {
            display: none;
          }
          span {
            transition: color 0.3s;
          }
        }
      }
    }
    .atk-list-comments-wrap {
      > .atk-comment-wrap {
        border-bottom: 1px dashed var(--main-card-border);
        .atk-header {
          .atk-badge {
            color: var(--at-color-bg);
            background-color: var(--at-color-main) !important;
          }
        }
        .atk-content {
          user-select: text;
          img {
            width: auto;
            max-width: 240px;
          }
          code {
            margin: 4px;
            vertical-align: inherit;
            border-radius: 6px;
          }
          pre {
            code {
              border-radius: 8px;
            }
          }
          blockquote {
            border-left: 8px solid var(--main-card-border);
            background-color: var(--main-card-second-background);
            border-radius: 4px 8px 8px 4px;
          }
        }
        &:last-child {
          border-bottom: none;
        }
      }
    }
    .atk-height-limit {
      .atk-height-limit-btn {
        height: 46px;
        width: calc(100% - 20px);
        display: flex;
        align-items: center;
        justify-content: center;
        padding: 12px 0;
        border-radius: 16px;
        transition:
          color 0.3s,
          background-color 0.3s;
        &:hover {
          color: var(--main-card-background);
          background-color: var(--main-color);
        }
      }
      &::after {
        height: 100px;
      }
    }
  }
  :deep(.atk-list-body) {
    .atk-pagination {
      .atk-input,
      .atk-btn {
        width: 40px;
        height: 40px;
        border-radius: 8px;
        overflow: hidden;
        background-color: var(--main-card-background);
        border: 1px solid var(--main-card-border);
        box-shadow: 0 8px 16px -4px var(--main-border-shadow);
        transition:
          width 0.3s,
          border-color 0.3s,
          box-shadow 0.3s;
        cursor: pointer;
        &:hover {
          border-color: var(--main-color);
          box-shadow: 0 8px 16px -4px var(--main-color-bg);
          svg {
            color: var(--main-color);
          }
        }
      }
      .atk-input {
        font-size: 1rem;
        &:hover,
        &:focus {
          width: 200px;
          border-color: var(--main-color);
          box-shadow: 0 8px 16px -4px var(--main-color-bg);
        }
      }
    }
  }
}
</style>
