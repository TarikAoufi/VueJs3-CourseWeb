<template>
  <div v-if="currentCourse" class="edit-form">
    <h4>Course</h4>
    <form>
      <div class="form-group">
        <label for="title">Title</label>
        <input type="text" class="form-control" id="title"
          v-model="currentCourse.title"
        />
      </div>
      <div class="form-group">
        <label for="description">Description</label>
        <input type="text" class="form-control" id="description"
          v-model="currentCourse.description"
        />
      </div>

      <div class="form-group">
        <label><strong>Status:</strong></label>
        {{ currentCourse.animated ? "Animated" : "Suspended" }}
      </div>
    </form>

    <button class="badge badge-primary mr-2"
      v-if="currentCourse.animated"
      @click="updateAnimated(false)"
    >
      Suspend

    </button>
    <button v-else class="badge badge-primary mr-2"
      @click="updateAnimated(true)"
    >
      Animate
    </button>

    <button class="badge badge-danger mr-2"
      @click="deleteCourse"
    >
      Delete
    </button>

    <button type="submit" class="badge badge-success"
      @click="updateCourse"
    >
      Update
    </button>
    <p>{{ message }}</p>
  </div>

  <div v-else>
    <br />
    <p>Please click on a Course...</p>
  </div>
</template>

<script>
import CourseDataService from "../services/CourseDataService";

export default {
  name: "course",
  data() {
    return {
      currentCourse: null,
      message: ''
    };
  },
  methods: {
    getCourse(id) {
      CourseDataService.get(id)
        .then(response => {
          this.currentCourse = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });
    },

    updateAnimated(status) {
      var data = {
        id: this.currentCourse.id,
        title: this.currentCourse.title,
        description: this.currentCourse.description,
        animated: status
      };

      CourseDataService.update(this.currentCourse.id, data)
        .then(response => {
          console.log(response.data);
          this.currentCourse.animated = status;
          this.message = 'The status was updated successfully!';
        })
        .catch(e => {
          console.log(e);
        });
    },

    updateCourse() {
      CourseDataService.update(this.currentCourse.id, this.currentCourse)
        .then(response => {
          console.log(response.data);
          this.message = 'The course was updated successfully!';
        })
        .catch(e => {
          console.log(e);
        });
    },

    deleteCourse() {
      CourseDataService.delete(this.currentCourse.id)
        .then(response => {
          console.log(response.data);
          this.$router.push({ name: "courses" });
        })
        .catch(e => {
          console.log(e);
        });
    }
  },
  mounted() {
    this.message = '';
    this.getCourse(this.$route.params.id);
  }
};
</script>

<style>
.edit-form {
  max-width: 300px;
  margin: auto;
}
</style>