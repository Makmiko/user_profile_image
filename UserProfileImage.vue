<template>
  <div class="profile-avatar">
    <img
        v-if="(imageSrc) && (!loadFail)"
        @error="failedLoad"
        :style="{width, height}"
        class="profile-avatar__avatar"
        :src="imageSrc"
        alt="user avatar"
    >
    <div
        v-else-if="user.full_name && userInitials"
        :style="{ width, height }"
        class="profile-avatar__user-info"
    >
      <div
          :style="{
        'background-color': stringToColor,
        'font-size': `${size.height - size.height/1.8}px`,
      }"
          class="profile-avatar__user-info__avatar" ref="avatar"
      >
        {{ userInitials }}
      </div>
    </div>
    <img
        :style="{width, height}"
        v-else src="../../assets/img/empty-profile.svg"
        alt="">
  </div>
</template>

<script>
export default {
  name: 'UserProfileImage',
  props: {
    user: {
      type: Object,
      default: () => ({}),
    },
    width: {
      type: String,
      default: '45px',
    },
    height: {
      type: String,
      default: '45px',
    },
    src: {
      type: String,
      default: '',
    },
    format: {
      type: String,
      default: '140x140',
    },
  },
  data() {
    return {
      loadFail: false,
    };
  },
  computed: {
    imageSrc() {
      let src = `//${this.src}`;
      const index = src.indexOf('{size}');
      if (index !== -1) {
        src = src.split('');
        src.splice(index, 6, this.format);
        return src.join('');
      }
      return '';
    },
    size() {
      const width = parseFloat(this.width);
      const height = parseFloat(this.height);
      if (Number.isFinite(width) && Number.isFinite(height)) {
        return { width, height };
      }
      return null;
    },
    stringToColor() {
      try {
        const username = this.user.full_name.split('');
        let hash = 0;
        let color = '#';
        let value;
        username.forEach((char) => {
          hash = char.charCodeAt(0) + ((hash << 9) - hash);
        });
        for (let i = 0; i < 3; i += 1) {
          value = (hash >> (i * 8)) & 0xFF;
          color += (`00${value.toString(16)}`).substr(-2);
        }
        return color;
      } catch (e) {
        return '#333333';
      }
    },
    userInitials() {
      try {
        let initials = '';
        if (this.user && (!this.user.full_name || this.user.full_name.match(/^\d*$/))) {
          return '';
        }
        this.user.full_name.split(' ').forEach((initial) => {
          if (!initial || initials.length >= 3) return;
          initials += initial[0];
        });
        return initials.toUpperCase();
      } catch (e) {
        return '';
      }
    },
  },
  methods: {
    failedLoad() {
      this.loadFail = true;
    },
  },
};
</script>

<style scoped>
.profile-avatar,
.profile-avatar__avatar {
  display: block;
  border-radius: 50%;
}
.profile-avatar__user-info {
  border-radius: 50%;
}
.profile-avatar__user-info__avatar {
  display: flex;
  border-radius: 50%;
  width: 100%;
  height: 100%;
  line-height: 30px;
  justify-content: center;
  align-items: center;
  color: white;
}
</style>
