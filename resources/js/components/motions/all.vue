<template>
  <main role="main" class="main-content">
    <div v-if="loading">
      <div><loadingPage /></div>
    </div>
    <div class="container-fluid">
      <h2 class="h5 page-title pb-5">كل الفيديوهات</h2>

      <table class="table mt-5 table-hover">
        <thead style="background-color: #0c88c8">
          <tr>
            <th scope="col">#</th>
            <th scope="col">الإسم</th>
            <th scope="col">الرابط</th>
            <th scope="col"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(motion, index) in motions" :key="motion.id">
            <td scope="row">{{ index + 1 }}</td>
            <td>{{ motion.title }}</td>
            <td><a :href="motion.link" target="_blank">إضغط هنا</a></td>
            <td class="actions">
              <router-link :to="{ name: 'edit_motion', params: { id: motion.id } }">
                <button type="button"><i class="fe fe-edit fe-16"></i></button
              ></router-link>
              <button type="button" @click="delCat(motion.id)">
                <i class="fe fe-trash fe-16"></i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <!-- pagination -->
      <nav aria-label="Page navigation example">
        <ul class="pagination justify-content-end">
          <li
            class="page-item"
            v-for="link in pagination.links"
            :key="link"
            v-bind:class="[{ disabled: !link.url }, { haha: link.active }]"
          >
            <a
              class="page-link"
              href="#"
              v-html="link.label"
              @click="fetchmotions(link.url)"
            ></a>
          </li>
        </ul>
      </nav>
    </div>
  </main>
</template>

<script>
import loadingPage from "../layouts/laoding.vue";
import axios from "axios";

export default {
  name: "motions",
  components: { loadingPage },
  data() {
    return {
      motions: [],
      loading: false,
      pagination: {},
    };
  },
  mounted() {
    this.fetchmotions();
  },
  methods: {
    delCat(id) {
      this.$swal
        .fire({
          title: "هل انت متأكد؟",
          text: "لن تتمكن من إعادة هذه الخطوة!",
          icon: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "نعم إحذف",
          cancelButtonText: "الغاء",
        })
        .then((result) => {
          if (result.isConfirmed) {
            axios.post(`/api/dash/motion/destroy/${id}`);
            this.$swal.fire("تم!", "تم الحذف بنجاح", "success");
            this.fetchmotions();
          }
        });
    },

    async fetchmotions(page_url) {
      this.loading = true;
      page_url = page_url || `api/dash/motions`;
      await axios
        .get(page_url)
        .then((res) => {
          this.motions = res.data.data;
          this.makePagination(res.data.meta);
        })
        .catch(() => {
          this.$router.push({ name: "serverErr" });
        });
      this.loading = false;
    },

    async makePagination(meta) {
      let pagination = {
        links: meta.links,
      };
      this.pagination = pagination;
    },
  },
};
</script>

<style></style>
