<script>
    import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
    import { Head, Link, useForm } from '@inertiajs/vue3';
    export default {
        components: {
            AuthenticatedLayout,
            Head,
            Link,
        },
        props:{
            feedbackData:Object,
            userRecords:Object,
            feedbackCommentsData:Array,
        },
        data() {
            return {
                finalUserArray:[],
                users: this.finalUserArray,
                showSuggestions: false,
            };
        },
        setup(props) {
            const form = useForm({
            feedback_id:props.feedbackData.id,
            comment:'',
            });
            return { form };
        },
        computed: {
            filterUserNameFormArray(){
            const names = this.userRecords.map(item => item.name);
            this.finalUserArray = names;
            },
            filteredUsers() {
            const match = this.form.comment.match(/@(\w*)$/);
            if (!match) return [];
            return this.finalUserArray.filter(u => u.toLowerCase().startsWith(match[1].toLowerCase()));
            },
        },
        methods: {
            checkForMention() {
                const hasMention = /@\w*$/.test(this.form.comment);
                this.showSuggestions = hasMention;
            },
            addMention(user) {
                const parts = this.form.comment.split(/@(\w*)$/);
                this.form.comment = `${parts[0]}${user} `;
                this.showSuggestions = false;
            },
            formattedDate(date) {
                const parsedDate = new Date(date);
                        const monthNames = [
                    "January", "February", "March", "April", "May", "June",
                    "July", "August", "September", "October", "November", "December"
                ];

                const year = parsedDate.getFullYear();
                const monthIndex = parsedDate.getMonth();
                const monthName = monthNames[monthIndex];
                const day = parsedDate.getDate();
                const hours = parsedDate.getHours();
                const minutes = parsedDate.getMinutes();
                const seconds = parsedDate.getSeconds();

                const formattedDay = day < 10 ? `0${day}` : day;
                const formattedHours = hours < 10 ? `0${hours}` : hours;
                const formattedMinutes = minutes < 10 ? `0${minutes}` : minutes;
                const formattedSeconds = seconds < 10 ? `0${seconds}` : seconds;

                return `${formattedDay} ${monthName} ${year} ${formattedHours}:${formattedMinutes}:${formattedSeconds}`;
            },
            submitComment()
            {
                this.form.post(route('submit.comment'));
                this.form.comment = '';
            }
        }
    };
</script>
<template>
  <Head title="Dashboard"></Head>
  <AuthenticatedLayout>
    <input type="hidden" :value="filterUserNameFormArray" />
    <section class="intro mt-5" style="background-color: white;">

        <div class="mask d-flex align-items-center h-100">
            <div class="container-fluid">
                <div class="row justify-content-center">
                    <div class="col-12 mb-5">
                        <div class="card" style="background-color: white;">
                            <div class="card-body">
                                <div class="card">
                                    <div class="card-header">
                                        {{ feedbackData.title }}
                                    </div>
                                    <div class="card-body">
                                    <blockquote class="blockquote mb-0">
                                        <p>{{ feedbackData.description }}</p>
                                        <footer class="blockquote-footer" style="font-size: 14px;">
                                            Author: {{ feedbackData.user_data.name }}
                                            <br>
                                            Time: {{ formattedDate(feedbackData.created_at) }}
                                        </footer>
                                    </blockquote>
                                    </div>
                                </div>

                                <div class="mt-3">
                                    <label>Add Your Comments </label>
                                    <span v-show="form.errors" style="color:red">{{form.errors.comment}}</span>
                                    <textarea class="form-control" v-model="this.form.comment" @input="checkForMention"></textarea>
                                    <div v-if="showSuggestions" class="mt-2">
                                    <ul class="list-group">
                                        <li v-for="(user, index) in filteredUsers" :key="index" @click="addMention(user)" class="list-group-item list-group-item-action">
                                        {{ user }}
                                        </li>
                                    </ul>
                                    </div>
                                </div>
                                <button type="submit" @click="submitComment" style="background-color: #007bff;" class="btn btn-primary btn-sm mt-3">Submit Comment</button>
                                <div class="card mt-5" v-if="this.feedbackCommentsData.length > 0" >
                                    <div class="card-header">
                                    All Comments
                                    </div>
                                    <div class="card-body" v-for="feedbackList,index in this.feedbackCommentsData">
                                    <blockquote class="blockquote mb-0">
                                        <footer class="blockquote-footer" style="font-size: 14px;">
                                            Name: {{ feedbackList.user_data.name }}
                                            <br>
                                            Time: {{ formattedDate(feedbackList.created_at) }}
                                        </footer>
                                        <p>{{ feedbackList.comment }}</p>
                                        <hr>
                                    </blockquote>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </section>
  </AuthenticatedLayout>
</template>
