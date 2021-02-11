<template>
    <div class="hotel" v-if="hotel">
        <div class="info">
            <p class="title">Name:</p>
            <p class="text">{{hotel.name}}</p>
        </div>
        <div class="info">
            <p class="title">Price:</p>
            <p class="text">{{hotel.price}} AED</p>
        </div>
        <div class="info">
            <p class="title">City:</p>
            <p class="text">{{hotel.city}}</p>
        </div>
    </div>
</template>

<script>
export default {
    props: ['hotel']
}
</script>

<style lang="scss" scoped>
    .hotel {
      padding: 1rem 0.5rem;
      margin-top: 1rem;
      border: 1px solid #424242;

      .info {
        display: flex;
        align-items: flex-start;
        margin-bottom: 0.5rem;

        .title {
          margin-right: 0.5rem;
          font-weight: 600;
        }

        p {
          margin-bottom: 0;
        }
      }
    }
</style>