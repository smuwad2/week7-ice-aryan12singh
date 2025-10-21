<script setup>
import axios from 'axios';
</script>

<script>
export default {
  data() {
    return {
      moods: ["Happy", "Sad", "Angry"],
      posts: [], // array of post objects
      entry: "",  // Stores entry being edited
      mood: "",   // Stores mood being edited
      showEditPost: false,  // Controls visibility of edit form
      editPostId: "",  // Stores ID of post being edited
    }
  },
  
  computed: {
    // Automatically detects if running on localhost or Codespaces
    baseUrl() {
      if (window.location.hostname == 'localhost')
        return 'http://localhost:3000'
      else {
        const codespace_host = window.location.hostname.replace('5173', '3000')
        return `https://${codespace_host}`;
      }
    }
  },
  
  created() {
    // Fetch posts when component is created
    axios.get(`${this.baseUrl}/posts`)
      .then(response => {
        // Store the posts array in Vue data
        this.posts = response.data
        console.log('Posts loaded:', this.posts);  // Debug
      })
      .catch(error => {
        this.posts = [{ entry: 'There was an error: ' + error.message }]
      })
  },
  
  methods: {
    // TODO: Show edit form when Edit button is clicked
    editPost(post) {
      console.log('Edit clicked for post:', post);  // Debug
      
      // Show the edit form
      this.showEditPost = true;
      
      // Store the post ID being edited
      this.editPostId = post.id;
      
      // Pre-fill the form with current values
      this.entry = post.entry;
      this.mood = post.mood;
      
      console.log('Edit form shown for post ID:', this.editPostId);  // Debug
    },
    
    // TODO: Update post when Update button is clicked
    updatePost(event) {
      event.preventDefault();  // Prevent form from refreshing page
      
      console.log('Updating post ID:', this.editPostId);  // Debug
      
      // Send PUT request to backend
      axios.put(`${this.baseUrl}/updatePost`, {
        id: this.editPostId,
        entry: this.entry,
        mood: this.mood
      })
      .then(response => {
        console.log('Post updated:', response.data);  // Debug
        
        // Update the post in the local posts array
        const post = this.posts.find(p => p.id == this.editPostId);
        if (post) {
          post.entry = this.entry;
          post.mood = this.mood;
        }
        
        // Hide the edit form
        this.showEditPost = false;
        
        // Clear the form fields
        this.entry = '';
        this.mood = '';
        this.editPostId = '';
      })
      .catch(error => {
        console.error('Error updating post:', error);
      });
    }
  }
}
</script>

<template>
  <div id="demo">
    <h2> View Blog Posts </h2>
    
    <table class="table m-2">
      <thead>
        <tr>
          <th>ID</th>
          <th>Entry</th>
          <th>Mood</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in posts" :key="post.id">
          <td>{{ post.id }}</td>
          <td>{{ post.entry }}</td>
          <td>{{ post.mood }}</td>
          <td>
            <!-- Call editPost with the post object -->
            <button @click="editPost(post)">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>
    
    <!-- Show form for editing post only when edit button is clicked -->
    <div id="editPost" v-if="showEditPost">
      <h3>Edit Post</h3>
      <div id="postContent" class="mx-3">
        <!-- Add @submit.prevent to call updatePost -->
        <form @submit.prevent="updatePost">
          <div class="mb-3">
            <label for="entry" class="form-label">Entry</label>
            <textarea id="entry" class="form-control" v-model="entry" required></textarea>
          </div>
          <div class="mb-3">
            <label for="mood" class="form-label">Mood</label>
            <select id="mood" class="form-select" v-model="mood" required>
              <option value="" disabled>Select Mood</option>
              <option v-for="m in moods" :key="m" :value="m">{{ m }}</option>
            </select>
          </div>
          <button type="submit" class="btn btn-primary">Update Post</button>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped></style>