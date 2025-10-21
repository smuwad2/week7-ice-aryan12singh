<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],  // Array to store all blog posts
      editingPostId: null,  // Track which post is being edited (null = none)
      editForm: {  // Store the values being edited
        entry: '',
        mood: ''
      },
      moods: ['Happy', 'Sad', 'Angry']  // Mood options
    }
  },
  
  created() {
    // Fetch posts when component is created
    this.fetchPosts();
  },
  
  methods: {
    // Fetch all posts from backend
    fetchPosts() {
      axios.get('http://localhost:3000/getPosts')
        .then(response => {
          this.posts = response.data;
        })
        .catch(error => {
          console.error('Error fetching posts:', error);
        });
    },
    
    // Show edit form for a specific post
    startEdit(post) {
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
    
    // Update the post using PUT request
    updatePost(postId) {
      axios.put('http://localhost:3000/updatePost', {
        id: postId,
        entry: this.editForm.entry,
        mood: this.editForm.mood
      })
      .then(response => {
        console.log('Post updated:', response.data);
        
        // Update the post in the local array
        const post = this.posts.find(p => p.id === postId);
        if (post) {
          post.entry = this.editForm.entry;
          post.mood = this.editForm.mood;
        }
        
        // Hide the edit form
        this.cancelEdit();
        
        alert('Post updated successfully!');
      })
      .catch(error => {
        console.error('Error updating post:', error);
        alert('Error updating post: ' + error.message);
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
        <tr v-for="post in posts" :key="post.id">
          <td>{{ post.id }}</td>
          <td>{{ post.entry }}</td>
          <td>{{ post.mood }}</td>
          <td>
            <button @click="startEdit(post)">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>
    
    <!-- Edit form (only shows when editing) -->
    <div v-if="editingPostId !== null" style="margin-top: 20px; padding: 20px; border: 2px solid #ccc;">
      <h3>Edit Post</h3>
      
      <label>Entry</label><br>
      <textarea v-model="editForm.entry" rows="5" cols="80"></textarea>
      <br><br>
      
      <label>Mood</label><br>
      <select v-model="editForm.mood">
        <option v-for="m in moods" :key="m">{{ m }}</option>
      </select>
      <br><br>
      
      <button @click="updatePost(editingPostId)">Update Post</button>
      <button @click="cancelEdit">Cancel</button>
    </div>
  </div>
</template>

<style scoped>
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