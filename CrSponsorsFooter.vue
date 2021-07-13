<template>
<section class="cr-sponsors-footer">
    <h2 class="center">BROUGHT TO YOU BY THE CTMA IN PARTNERSHIP WITH:</h2>
    <div class="logo-container">
        <img class="logo-container__logo" v-bind:src="ctmaLogo" alt="CTMA Logo">
        <img class="logo-container__logo" v-bind:src="octeLogo" alt="CTMA Logo">
        <img class="logo-container__logo" v-bind:src="mentorWorksLogo" alt="Mentor Works Logo">
    </div>
    <div class="logo-container">
        <img class="logo-container__govt" v-bind:src="govtLogos" alt="Province of Ontario Logo">
        <h4 class='center'>This Employment Ontario program is funded in part by the Government of Canada and the Government of Ontario.</h4>
    </div>
</section>

</template>
<script>
export default {
    name: "CrSponsorsFooter",

    data() {
        return {
            ctmaLogo: 'https://media.ctma.com/CTMA-logos/CTMA-Logo-Horizontal.jpg',
            octeLogo: 'https://media.ctma.com/career-ready/octe-logo_new.png',
            mentorWorksLogo: 'https://media.ctma.com/career-ready/mentor works logo.jpg',
            govtLogos: 'https://media.ctma.com/career-ready/EO+tri-wordmark+EN+COLOUR.png'
        }
    },
}
</script>
<style lang="scss" scoped>
.logo-container {
    display: flex;
    align-items: flex-start;
    flex-wrap: wrap;
    justify-content: space-evenly;
    margin-bottom: 1rem;

    &__logo {
        height: 5rem;
    }

    &__govt {
        height: 10rem;
    }
}
</style>