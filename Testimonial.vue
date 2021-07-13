<template>
    <section class='testimonial'>
        <div class='layout-container'>
            <img v-bind:src="imageLink" v-bind:alt="altText">
            <div class='inner-container'>
                <h4>{{ headingText }}</h4>
                <p class='sub-heading'>{{ subHeadingText }}</p>
                <p>{{ mainString }}</p>
            </div>
        </div>
    </section>
</template>
<script>
export default {
    name: 'testimonial',
    props: {
        imageLink: String,
        altText: String,
        headingText: String,
        subHeadingText: String,
        mainString: String
    },
}
</script>
<style lang="scss" scoped>
.testimonial {
    display: flex;
    align-items: center;
    margin: 1rem 0;
    justify-content: space-between;
    padding-bottom: 1rem;
    border-bottom: 2px solid $color-yellow;

    @include respond-to( 'tab-port' ) {
        flex-direction: column;
    }

    .layout-container {
        display: flex;
        align-items: center;
        width: 100%;

        @include respond-to( 'tab-port' ) {
            flex-direction: column;
            height: auto;
            max-width: 20rem;
        }

        img {
            margin: .2rem 2rem .8rem 2rem;
            max-width: 25rem;
            height: auto;

            @include respond-to( 'phone' ) {
                max-width: 20rem;
            }
        }
    }
}
</style>
