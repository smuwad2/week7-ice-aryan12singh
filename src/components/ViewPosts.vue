<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],  // Array to store all blog posts from backend
      editingPostId: null,  // Track which post is being edited (null = none)
      editForm: {  // Temporary storage for the values being edited
        entry: '',  // Stores the entry text while editing
        mood: ''    // Stores the mood while editing
      },
      moods: ['Happy', 'Sad', 'Angry']  // Available mood options for dropdown
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
    // Lifecycle hook: runs automatically when component is created
    this.fetchPosts();
  },
  
  methods: {
    // Fetch all posts from backend API
    fetchPosts() {
      axios.get(`${this.baseUrl}/posts`)
        .then(response => {
          this.posts = response.data;
          console.log('Posts loaded:', this.posts);
        })
        .catch(error => {
          console.error('Error fetching posts:', error);
        });
    },
    
    // Show edit form for a specific post
    startEdit(post) {
      console.log('Edit clicked for post:', post);
      this.editingPostId = post.id;
      this.editForm.entry = post.entry;
      this.editForm.mood = post.mood;
    },
    
    // Cancel editing and hide the form
    cancelEdit() {
      this.editingPostId = null;
      this.editForm.entry = '';
      this.editForm.mood = '';
    },
    
    // Update the post using POST request to backend
    updatePost(postId) {
      // POST request with id as query parameter (backend expects this format)
      axios.post(`${this.baseUrl}/updatePost?id=${postId}`, {
        entry: this.editForm.entry,
        mood: this.editForm.mood
      })
      .then(response => {
        console.log('Post updated:', response.data);
        
        // Update the post in the local array (so UI updates immediately)
        const post = this.posts.find(p => p.id == postId);
        if (post) {
          post.entry = this.editForm.entry;
          post.mood = this.editForm.mood;
        }
        
        // Hide the edit form
        this.cancelEdit();
      })
      .catch(error => {
        console.error('Error updating post:', error);
      });
    }
  }
}
</script>

<template>
  <div>
    <h2>Blog Posts</h2>
    
    <!-- Table of blog posts -->
    <table border="1" style="width: 100%; border-collapse: collapse;">
      <thead>
        <tr>
          <th>ID</th>
          <th>Entry</th>
          <th>Mood</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <!-- Loop through all posts and create a row for each -->
        <tr v-for="post in posts" :key="post.id">
          <td>{{ post.id }}</td>
          <td>{{ post.entry }}</td>
          <td>{{ post.mood }}</td>
          <td>
            <!-- When clicked, call startEdit with this specific post -->
            <button @click="startEdit(post)">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>
    
    <!-- Edit form (only shows when editing) -->
    <!-- v-if: Only render this div when editingPostId is NOT null -->
    <!-- id="editPost": Required by the test to find this element -->
    <div v-if="editingPostId !== null" id="editPost" style="margin-top: 20px; padding: 20px; border: 2px solid #ccc;">
      <h3>Edit Post</h3>
      
      <label>Entry</label><br>
      <!-- id="entry": Required by test -->
      <!-- v-model: Two-way binding with editForm.entry -->
      <textarea id="entry" v-model="editForm.entry" rows="5" cols="80"></textarea>
      <br><br>
      
      <label>Mood</label><br>
      <!-- id="mood": Required by test -->
      <!-- v-model: Two-way binding with editForm.mood -->
      <select id="mood" v-model="editForm.mood">
        <!-- Create an option for each mood in the moods array -->
        <option v-for="m in moods" :key="m">{{ m }}</option>
      </select>
      <br><br>
      
      <!-- Call updatePost when clicked, passing the current editingPostId -->
      <button @click="updatePost(editingPostId)">Update Post</button>
      
      <!-- Call cancelEdit to hide the form -->
      <button @click="cancelEdit">Cancel</button>
    </div>
  </div>
</template>

<style scoped>
/* Styles only apply to this component */

table {
  margin-top: 20px;
}

th, td {
  padding: 10px;
  text-align: left;
}

th {
  background-color: #f0f0f0;
}

button {
  margin-right: 5px;
  padding: 5px 10px;
  cursor: pointer;
}
</style>