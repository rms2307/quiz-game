<template>
  <div class="dropdown">
    <button @click="toggleDropdown">
      {{ this.title }}
    </button>
    <ul v-if="isOpen" class="dropdown-menu">
      <li
        v-for="(item, index) in items"
        :key="index"
        @click="selectOption(item)"
      >
        {{ item.name }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    title: String,
    items: [],
    loading: Boolean,
  },
  data() {
    return {
      isOpen: false,
      selectedOption: undefined,
    }
  },
  methods: {
    toggleDropdown() {
      this.isOpen = !this.isOpen
    },
    selectOption(item) {
      this.$emit("selectOption", item)
      this.toggleDropdown()
    },
  },
}
</script>

<style lang="scss">
.dropdown {
  position: relative;
  display: inline-block;
  margin: 5px;
}

.dropdown button {
  background-color: #3498db;
  color: white;
  border: none;
  cursor: pointer;
  padding: 10px 20px;
  width: 180px;
  width: 150px;
}

.dropdown-menu {
  position: absolute;
  background-color: white;
  border: 1px solid #ccc;
  list-style: none;
  padding: 0;
  margin: 0;
  width: 99%;
  max-height: 150px;
  overflow-y: auto;
}

.dropdown-menu li {
  padding: 10px;
  cursor: pointer;
}

.dropdown-menu li:hover {
  background-color: #f1f1f1;
}
</style>
