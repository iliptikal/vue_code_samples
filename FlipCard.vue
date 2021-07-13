<template>
    <div class="card">
        <div class="card__side front-side">
            <div class="front-photo"
                v-bind:style="{ backgroundImage: 'linear-gradient(0deg, rgba(6, 34, 71, .7) 0%, rgba(29, 28, 28, .35) 40% ), url(' + imageUrl + ')' }">
                &nbsp;
            </div>
            <h4 class="front-heading">
                <span class="heading-span"><slot name="front-heading"></slot></span>
            </h4>
            <div class="front-details">
                <slot name="front-details"></slot>
            </div>
        </div>
        <div class="card__side back-side">
            <slot name="back-details"></slot>
        </div>
    </div>
</template>

<script>

export default {
    name: "flip-card",
    props: {
        imageUrl: String
    }
};
</script>

<style lang="scss" scoped>
.card {
    // FUNCTIONALITY
    perspective: 150rem;
    -moz-perspective: 150rem;
    position: relative;
    width: 100%;
    height: 20rem;

    & > .card__side {
        font-size: 2rem;
        transition: all .8s ease;
        position: absolute;
        width: 100%;
        height: 20rem;
        top: 0;
        left: 0;
        width: 100%;
        -webkit-backface-visibility: hidden;
        backface-visibility: hidden;
        overflow: hidden;
        box-shadow: 0 .5rem 1rem rgba( $color-black, .05 );
    }

    & > .front-side {
        background-color: $color-white;

        & > .front-photo {
            background-size: 100%;
            background-position: 0% 18%; //horizontal then vertical
            clip-path: polygon( 0 0, 100% 0, 100% 85%, 0 100% );
            -webkit-clip-path: polygon( 0 0, 100% 0, 100% 85%, 0 100% );
            height: 11.5rem;
        }

        & > .front-heading {
            text-transform: uppercase;
            text-align: right;
            color: $color-white;
            position: absolute;
            top: 2rem;
            right: 2rem;
            width: 68%;

            @include respond-to( 'tab-land' ){
                font-size: 1.5rem;
            }
        }

        & > .front-details {
            position: absolute;
            top: 12rem;
            left: 2rem;
            right: 2rem;
        }
    }

    & > .back-side {
        padding: 0 1.5rem 0 1.5rem;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        transform: rotateY( 180deg ) !important;
        background-image: linear-gradient( to right bottom, $color-primary, $color-secondary );
    }

    &:hover > .front-side {
        transform: rotateY( -180deg ) !important;
    }

    &:hover > .back-side {
        transform: rotateY( 0 ) !important;

        @include respond-to ( 'phone' ) {
            transform: translateY( -1.5rem ) !important;
        }
    }
}

@media screen and ( hover: none ),
screen and ( max-width: 37.5em ) {
    .card {
        height: 44.5rem;
        background-color: $color-white;
        box-shadow: 0 1.5rem 4rem rgba( $color-black, .15 );

        &:focus {
            & > .back-side {
                transform: rotateY( 0 ) translateY( 10rem ) !important;
            }
        }

        @include respond-to ( 'phone' ) {
            height: 38.5rem;
        }

        & > .card__side {
            height: 23rem;
            position: relative;
            box-shadow: none;
            transition: none;

            @include respond-to ( 'phone' ) {
                height: 20rem;
            }
        }

        & > .front-side {
            background-color: $color-white;

            & > .front-photo {
                height: 11rem;

                @include respond-to ( 'phone' ) {
                    height: 8rem;
                }
            }

            & > .front-details {
                padding: 1rem 2rem 0rem 2rem;
                left: 1rem;
                right: 1rem;

                @include respond-to ( 'tab-land' ) {
                    top: 12rem;
                }

                @include respond-to ( 'phone' ) {
                    top: 9rem;
                }
            }
        }

        & > .back-side {
            transform: rotateY( 0 ) translateY( -1.5rem ) !important;
            clip-path: polygon( 0 10%, 100% 0, 100% 100%, 0 100% );
        }
        &:hover > .front-side {
            transform: rotateY( 0 ) !important;
        }

        &:hover > .back-side {
            transform: rotateY( 0 ) translateY( -1.5rem ) !important;
        }
    }
}
</style>
