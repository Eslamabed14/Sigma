<template>
  <main role="main" class="main-content">
    <div v-if="loading">
      <div><loadingPage /></div>
    </div>
    <div class="container-fluid">
      <form @submit.prevent="saveForm">
        <div class="card shadow mb-4">
          <div class="card-header">
            <strong class="card-title">تعديل {{ form.name }}</strong>
          </div>
          <div class="card-body">
            <div class="row">
              <div class="col-md-6 align-self-center">
                <div class="form-group mb-3">
                  <label for="simpleinput">الإسم</label>
                  <input
                    type="text"
                    id="simpleinput"
                    class="form-control"
                    v-model="this.form.name"
                    required
                  />
                </div>
                <div class="form-group mb-3">
                  <label for="example-email">الصورة</label>
                  <input
                    type="file"
                    id="example-email"
                    name="example-email"
                    class="form-control"
                    ref="file"
                    @change="selectFile"
                  />
                </div>
                <div class="form-group mb-3">
                  <label for="simpleinput">الوظيفة</label>
                  <input
                    type="text"
                    id="simpleinput"
                    class="form-control"
                    v-model="this.form.job"
                  />
                  <span class="text-danger" v-if="errors.job">{{ errors.job[0] }}</span>
                </div>
                <div class="form-group mb-3">
                  <label for="simpleinput">لينكدان</label>
                  <input
                    type="text"
                    id="simpleinput"
                    class="form-control"
                    v-model="this.form.linkedin"
                  />
                </div>
                <div class="form-group mb-3">
                  <label for="simpleinput">فيسبوك</label>
                  <input
                    type="text"
                    id="simpleinput"
                    class="form-control"
                    v-model="this.form.facebook"
                  />
                </div>
                <div class="form-group mb-3">
                  <label for="simpleinput">انستاجرام</label>
                  <input
                    type="text"
                    id="simpleinput"
                    class="form-control"
                    v-model="this.form.instagram"
                  />
                </div>
                <div class="form-group mb-3">
                  <label for="simpleinput">تويتر</label>
                  <input
                    type="text"
                    id="simpleinput"
                    class="form-control"
                    v-model="this.form.twitter"
                  />
                </div>
                <button
                  type="submit"
                  class="btn"
                  style="
                    background-color: #0c88c8;
                    color: aliceblue;
                    width: 100px;
                    font-weight: 600;
                  "
                >
                  تأكيد
                </button>
              </div>
              <!-- /.col -->
              <div class="col-md-6 align-self-center">
                <img :src="this.form.image" class="img-thumbnail" alt="" />
              </div>
            </div>
          </div>
        </div>
      </form>
    </div>
  </main>
</template>

<script>
import loadingPage from "../layouts/laoding.vue";

export default {
  name: "edit_team",
  components: { loadingPage },
  data() {
    return {
      loading: false,
      form: {
        name: "",
        image: "",
        job: "",
        linkedin: "",
        facebook: "",
        instagram: "",
        twitter: "",
      },
      errors: [],
      id: this.$route.params.id,
    };
  },
  mounted() {
    this.fetchCategory();
  },
  methods: {
    async fetchCategory() {
      this.loading = true;
      await axios
        .get(`/api/dash/team/show/${this.id}`)
        .then((res) => {
          this.form = res.data.team;
        })
        .catch(() => {
          this.$router.push({ name: "error404" });
        });
      this.loading = false;
    },
    alert() {
      var toastMixin = this.$swal.mixin({
        toast: true,
        icon: "success",
        title: "General Title",
        animation: false,
        position: "top-right",
        showConfirmButton: false,
        timer: 3000,
        timerProgressBar: true,
        didOpen: (toast) => {
          toast.addEventListener("mouseenter", this.$swal.stopTimer);
          toast.addEventListener("mouseleave", this.$swal.resumeTimer);
        },
      });
      toastMixin.fire({
        animation: true,
        title: "تم تعديل العضو بنجاح",
      });
    },
    async saveForm() {
      this.loading = true;
      await axios
        .post(`/api/dash/team/update/${this.id}`, this.form, {
          headers: {
            Accept: "application/json",
            "Content-Type": "multipart/form-data",
          },
        })
        .then(() => {
          this.fetchCategory();
          this.alert();
        })
        .catch((error) => {
          console.log(error);
        });
      this.loading = false;
    },

    selectFile() {
      this.form.image = this.$refs.file.files[0];
    },
  },
};
</script>

<style></style>
