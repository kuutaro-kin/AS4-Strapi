<template>
  <div v-if="error">
    {{ error }}
  </div>
  <table class="table-auto">
    <thead>
      <tr>
        <th
          class="px-6 py-3 border-b border-gray-200 bg-gray-100 text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">
          Name
        </th>
        <th
          class="px-6 py-3 border-b border-gray-200 bg-gray-100 text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">
          Title
        </th>
        <th
          class="px-6 py-3 border-b border-gray-200 bg-gray-100 text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">
          User ID
        </th>
        <th
          class="px-6 py-3 border-b border-gray-200 bg-gray-100 text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">
          ID
        </th>

      </tr>
    </thead>
    <tbody>
      <tr v-for="(post, index) in posts.data" :key="post.id">
        <td class="px-6 py-4 border-b border-gray-200">
          <div class="flex items-center">
            <img class="h-12 w-12 rounded-full mr-4"
              :src="'http://localhost:1337' + blogusers.data[index].attributes.photo.data.attributes.url"
              alt="User Avatar">
            <span>{{ blogusers.data[index].attributes.username }}</span>
          </div>
        </td>
        <td class="px-6 py-4 border-b border-gray-200">
          <a :href="'/PostList/' + post.attributes.postID">{{ post.attributes.title
            }}</a>
          <!-- <router-link :to="'/PostList/' + post.attributes.postID">{{ post.attributes.title }}</router-link> -->
        </td>
        <td class="px-6 py-4 border-b border-gray-200">{{ blogusers.data[index].attributes.studentID }}</td>
        <td class="px-6 py-4 border-b border-gray-200">{{ post.attributes.postID }}</td>

      </tr>
    </tbody>

  </table>
  <button class="bg-indigo-500 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded-full" @click="openForm">
    Add User
  </button>

  <div v-if="showForm" class="fixed z-10 inset-0 overflow-y-auto" aria-labelledby="modal-title" role="dialog"
    aria-modal="true">
    <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" aria-hidden="true"></div>

      <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>

      <div
        class="inline-block align-bottom bg-gradient-to-r from-indigo-900 via-indigo-500 to-indigo-900 rounded-[15px] text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
        <div class="bg-grey px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
          <form @submit.prevent="addUser">
            <label for="studentid" style="color: white;">Student ID: </label>
            <input type="text" id="studentid" v-model="newUser.studentid" required
              class="w-full rounded-full pl-2 py-1">
            <br><br>
            <label for="username" style="color: white;">Username: </label>
            <input type="text" id="username" v-model="newUser.username" required class="w-full rounded-full pl-2 py-1">
            <br><br>
            <label for="fullname" style="color: white;">Full Name: </label>
            <input type="text" id="fullname" v-model="newUser.fullname" required class="w-full rounded-full pl-2 py-1">
            <br><br>
            <div class="flex justify-center">
              <button type="submit" class="bg-blue-900 hover:bg-blue-950 text-white font-bold py-2 px-4 rounded-full">
                Submit
              </button>
            </div>
          </form>
        </div>
        <div class="bg-black-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
          <button type="button"
            class="bg-white rounded-full px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500"
            @click="showForm = false">
            Close
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
export default {
  name: 'App',
  data() {
    return {
      posts: [],
      blogusers: [],
      error: null,
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + import.meta.env.VITE_STRAPI_TOKEN
      },
      showForm: false,
      newUser: {
        studentid: '',
        username: '',
        fullname: ''
      }
    }
  },
  methods: {
    parseJSON: function (resp) {
      return (resp.json ? resp.json() : resp);
    },
    checkStatus: function (resp) {
      if (resp.status >= 200 && resp.status < 300) {
        return resp;
      }
      return this.parseJSON(resp).then((resp) => {
        throw resp;
      });
    },
    openForm(index) {
      this.showForm = true;
    },
    addUser() {
      // Perform the logic to add the user to Strapi
      // Use this.newUser.studentid, this.newUser.username, this.newUser.fullname to access the form values
      // Make a POST request to the Strapi API with the necessary data
      // Handle any errors that occur during the request
      // Reset the form values and hide the form after successful submission
    }
  },

  async mounted() {
    try {
      // Fetch blog users with populated photos
      const blogUsersResponse = await fetch("http://localhost:1337/api/blogusers?populate=photo", {
        method: 'GET',
        headers: this.headers,
      });
      this.checkStatus(blogUsersResponse); // Handle potential status errors
      this.blogusers = await this.parseJSON(blogUsersResponse); // Parse response to JSON

      // Fetch posts
      const postsResponse = await fetch("http://localhost:1337/api/posts", {
        method: 'GET',
        headers: this.headers,
      });
      this.checkStatus(postsResponse); // Handle potential status errors
      this.posts = await this.parseJSON(postsResponse); // Parse response to JSON
    } catch (error) {
      this.error = error; // Handle errors during data fetching
      console.error('Error fetching data:', error); // Log the error for debugging
    }
  }

};
</script>