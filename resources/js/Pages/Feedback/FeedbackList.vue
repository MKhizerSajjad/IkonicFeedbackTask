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
        },
        methods: {
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
            }
        }
    };
</script>

<template>
  <Head title="Dashboard"></Head>

  <AuthenticatedLayout>
    <section class="intro mt-5" style="background-color: white;">
      <div class="container row">
        <div class="col-md-6 col-6 mt-3 font-weight-bold"  style="font-size: 28px;">Feedback List</div>
        <div class="col-md-6 col-6 text-end mt-3">
          <Link :href="route('create.feedback')" class="btn btn-light-primary font-weight-bold ml-2"
           style="background-color: #007bff; color: white" >Create Feedback</Link>
        </div>
      </div>

      <!-- Table Section -->
      <div class="bg-image h-100 mt-3 " >
        <div class="mask d-flex align-items-center h-100">
          <div class="container">
            <div class="row justify-content-center">
              <div class="col-12 mb-5">
                <div class="card" style="background-color: white;">
                  <div class="card-body">
                    <div class="table-responsive">
                      <table class="table table-hover mb-0" style="background-color: white;">
                        <thead>
                          <tr>
                            <th scope="col">Sr#</th>
                            <th scope="col">Title</th>
                            <th scope="col">Description</th>
                            <th scope="col">Author</th>
                            <th scope="col">Date Time</th>
                            <th scope="col">Action</th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr v-for="feedbackList,index in this.feedbackData.data" :key="feedbackList.id">
                            <th scope="row"> {{ index + 1 }}</th>
                            <td> {{ feedbackList.title }}</td>
                            <td> {{ feedbackList.description }}</td>
                            <td> {{ feedbackList.user_data?.name }}</td>
                            <td> {{ formattedDate(feedbackList.created_at) }}</td>
                            <td>
                              <Link :href="route('feedback.comment',{'feedback_id':feedbackList.id})" style="background-color: #007bff;" class="btn btn-primary btn-sm">
                                Make Comment
                              </Link>
                            </td>
                          </tr>
                        </tbody>
                      </table>
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
