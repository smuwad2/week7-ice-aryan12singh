<script>
    import axios from 'axios';
    export default { 

       // add code here
        data() {
            return {
                subject: '',
                entry: '',
                mood: 'Happy',  // Default mood
                moods: ['Happy', 'Sad', 'Angry']  // List of moods for dropdown
            }
        },
        methods: {
            submitPost() {
                // Make Axios GET request with params
                axios.get('http://localhost:3000/addPost', {
                params: {
                    subject: this.subject,
                    entry: this.entry,
                    mood: this.mood
                }
                })
                .then(response => {
                console.log('Post added successfully:', response.data);
                alert('Post added successfully!');
                
                // Clear the form after successful submission
                this.subject = '';
                this.entry = '';
                this.mood = 'Happy';
                })
                .catch(error => {
                console.error('Error adding post:', error);
                alert('Error adding post: ' + error.message);
                });
            }
        }
    }
</script>

<template>
    <div class="table m-2">
        <h3>Add a New Blog Post</h3>

        Subject: <input type='text' size='30' v-model='subject' required>
        <br>

        Entry: <br>
        <textarea name='entry' cols='80' rows='5' v-model='entry' required></textarea>
        <br>

        Mood:
        <!-- TODO: Build a dropdown list here for selecting the mood -->
        <select v-model="mood">
        <option v-for="m in moods" :key="m">{{ m }}</option>
        </select>
        <br><br>

        <br>

        <br>
        <button @click="submitPost">Submit New Post</button>

        <hr> Click  <a><router-link to="/ViewPosts/">here</router-link></a>  to return to Main Page
       
    </div>
</template>

