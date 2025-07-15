<template>
  <div class="container mt-4">
    <div class="row mb-3">
      <div class="col">
        <InputSearch v-model="searchText" />
      </div>
    </div>

    <div class="row">
      <!-- DANH SÁCH -->
      <div class="col-md-6 mb-3">
        <h4>Danh bạ <i class="fas fa-address-book"></i></h4>
        <ContactList
          v-if="filteredContactsCount > 0"
          :contacts="filteredContacts"
          v-model:activeIndex="activeIndex"
        />
        <p v-else>Không có liên hệ nào.</p>

        <div class="mt-3 d-flex flex-wrap gap-2">
          <button class="btn btn-primary btn-sm" @click="refreshList">
            <i class="fas fa-redo"></i> Làm mới
          </button>
          <button class="btn btn-success btn-sm" @click="goToAddContact">
            <i class="fas fa-plus"></i> Thêm mới
          </button>
          <button class="btn btn-danger btn-sm" @click="removeAllContacts">
            <i class="fas fa-trash"></i> Xóa tất cả
          </button>
        </div>
      </div>

      <!-- CHI TIẾT -->
      <div class="col-md-6 mb-3" v-if="activeContact">
        <h4>Chi tiết Liên hệ <i class="fas fa-address-card"></i></h4>
        <ContactCard :contact="activeContact" />
        <router-link
          :to="{ name: 'contact.edit', params: { id: activeContact._id } }"
        >
          <span class="badge bg-warning text-dark mt-2" style="cursor: pointer;">
            <i class="fas fa-edit"></i> Hiệu chỉnh
          </span>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import ContactCard from "@/components/ContactCard.vue";
import ContactList from "@/components/ContactList.vue";
import InputSearch from "@/components/InputSearch.vue";
import ContactService from "@/services/contact.service";

export default {
  components: { ContactCard, ContactList, InputSearch },
  data() {
    return {
      contacts: [],
      activeIndex: -1,
      searchText: "",
    };
  },
  computed: {
    contactStrings() {
      return this.contacts.map((c) =>
        [c.name, c.email, c.address, c.phone].join("")
      );
    },
    filteredContacts() {
      if (!this.searchText) return this.contacts;
      return this.contacts.filter((_, i) =>
        this.contactStrings[i].includes(this.searchText)
      );
    },
    filteredContactsCount() {
      return this.filteredContacts.length;
    },
    activeContact() {
      return this.activeIndex >= 0
        ? this.filteredContacts[this.activeIndex]
        : null;
    },
  },
  methods: {
    async retrieveContacts() {
      try {
        this.contacts = await ContactService.getAll();
      } catch (err) {
        console.log(err);
      }
    },
    refreshList() {
      this.retrieveContacts();
      this.activeIndex = -1;
    },
    async removeAllContacts() {
      if (confirm("Bạn có chắc muốn xóa tất cả?")) {
        try {
          await ContactService.deleteAll();
          this.refreshList();
        } catch (err) {
          console.log(err);
        }
      }
    },
    goToAddContact() {
      this.$router.push({ name: "contact.add" });
    },
  },
  mounted() {
    this.refreshList();
  },
};
</script>

<style scoped>
h4 {
  font-weight: bold;
}
</style>
