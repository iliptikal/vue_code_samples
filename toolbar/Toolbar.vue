<template>
    <div class="navigation">
        <input
            type="checkbox"
            class="navigation__checkbox"
            id="navigation-toggle"
        />
        <label for="navigation-toggle" class="navigation__button">
            <span class="navigation__icon">&nbsp;</span>
        </label>

        <div class="navigation__background">&nbsp;</div>

        <div id='scroll-container'>
            <nav class="navigation__toolbar" id='children-slot'>
                <slot></slot>
            </nav>
        </div>
    </div>
</template>
<script>
export default {
    name: "toolbar",
    data() {
        return {
            screenWidth: 0,
            savedHeight: 0, //stores the height of the mobile toolbar menu
            minHeight: 0 //used to calculate the height of the toolbar on desktop load
        };
    },
    watch: {
        //When resizing out of mobile mode the toolbar is giant - bugfix below using saved heights and disabling static css
        screenWidth: function( val ) {
            let container = this.$el.querySelector( '.navigation__toolbar' );
            if( val >= 1150 ) { //desktop - same value as the toolbar menu
                if( this.savedHeight === 0 && container.offsetHeight > this.minHeight ) {
                    this.savedHeight = container.offsetHeight;
                }
                container.style.height = null;
            } else {
                if( this.savedHeight !== 0 ) {
                    container.style.height = this.savedHeight + "px";
                    this.savedHeight = 0;
                }
            }
        } 
    },
    mounted() {
        this._addTransitionListener();
        this.$el.addEventListener( 'close-fullscreen-toolbar', ( e ) => { this.closeFullscreenMenu( e ) } );
        this.$el.querySelector( '.navigation__button' ).addEventListener( 'click', ( e ) => {
            if( !this.$el.querySelector( '#navigation-toggle' ).checked ) {
                this._dispatchSupressScroll( true );
            } else {
                this._dispatchSupressScroll( false );
            }
        } );

        //Code below is for resizing bugs that go with static js heights
        this.screenWidth = this.$el.offsetWidth;
        let resizeObserver = new ResizeObserver( () => {
            this.screenWidth = this.$el.offsetWidth;
        } );
        resizeObserver.observe( this.$el );

        let container = this.$el.querySelector( '.navigation__toolbar' );
        if( this.$el.offsetWidth >= 1150 ) {
            this.minHeight = container.offsetHeight;
        }
    },
    methods: {
        _addTransitionListener() {
            this.$el.querySelector( '.navigation__toolbar' ).addEventListener( 'nested-transition', ( e ) => {
                //only nested
                //when the transition starts change our height based on what the child is doing: expanding/contracting
                let height = this.$el.querySelector( '.navigation__toolbar' ).offsetHeight;
                if( e.detail.visible ) {
                    height += e.detail.height;
                } else {
                    height -= e.detail.height
                }
                this._transition( height );
            } );
        },

        _transition( height ) {
            let container = this.$el.querySelector( '.navigation__toolbar' );
            let origHeight = container.offsetHeight;
            let transition = container.style.transition;
            container.style.transition = '';

            requestAnimationFrame( () => {
                container.style.height = origHeight + 'px';
                container.style.transition = transition;

                requestAnimationFrame( ()=> {
                    container.style.height = height + 'px';
                } );
            } );
        },

        closeFullscreenMenu() {
            this.$el.querySelector( "#navigation-toggle" ).checked = false;
            this._dispatchSupressScroll( false );
        },

        _dispatchSupressScroll( supress ) {    
            this.$el.dispatchEvent( new CustomEvent( 'supress-scroll', {
                bubbles: true,
                detail: {
                    'supress': supress
                }
            } ) );
        }
    }
};
</script>
<style lang="scss" scoped>
.navigation {
    font-size: .85rem;

    //fix toolbar to top of page/screen on scrolling
    position: -webkit-sticky;
    position: sticky;
    top: 0;
    z-index: 1000;
    -webkit-box-shadow: 0px 2px 15px 2px rgba( 29, 28, 28, .25 ); 
    box-shadow: 0px 2px 15px 2px rgba( 29, 28, 28, .25 );
    
    .navigation__toolbar {
        
        transition: all .2s ease-in-out;

        & > .toolbar-menu {
            position: relative;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
        }
    }

    @include respond-to( 'tab-land' ) {
            
        //stylings for mobile mode
        .navigation {
            z-index: 3000;
        }

        .navigation__toolbar {
            display: none;

            & > .toolbar-menu * {
                position: static;
                display: flex;
                flex-direction: column;
                justify-content: center;
                align-items: center;
                text-align: center;
                transform: translateX(-30%) !important;
            }

            & > a:first-of-type {
                margin-top: 5rem;
            }

            & > div:last-of-type {
                margin-bottom: 5rem;
            }
        }

        //functionality -- when the label is clicked, it checks the checkbox and shows the mobile menu
        .navigation__checkbox:checked ~ #scroll-container {

            overflow: auto;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;

            & > .navigation__toolbar {
                display: flex;
                flex-direction: column;
                justify-content: space-between;
                align-items: center;
                overflow: auto;
                z-index: 3000;
                height: 100%;
            }
        }

        .navigation__button {
            display: block;
            background-color: $color-white;
            height: 3rem;
            width: 3rem;
            position: fixed;
            top: 2rem;
            right: 1rem;
            border-radius: 50%;
            z-index: 4000;
            box-shadow: $shadow-primary;
            text-align: center;
            cursor: pointer;
        }

        //burger menu icon
        .navigation__icon {
            position: relative;
            margin-top: 1.5rem;

            &,
            &::before,
            &::after {
                width: 1.5rem;
                height: 2px;
                background-color: $color-black;
                display: inline-block;
            }

            &::before,
            &::after {
                content: "";
                position: absolute;
                left: 0;
                transition: all 0.2s;
            }

            &::before {
                top: -0.5rem;
            }

            &::after {
                top: 0.5rem;
            }
        }
    }

    //hide checkbox, as it's only used for functionality
    &__checkbox {
        display: none;
    }

    //defaults for non-mobile version of toolbar
    &__button {
        display: none;
    }

    &__background {
        display: none;
    }

    &__toolbar {
        box-sizing: border-box;
        background: $color-white;
        color: $color-grey-dark;
        display: flex;
        justify-content: center;
        font-family: $font-primary;

        & > * {
            border-top: 3px solid $transparent;
            padding: 0 0.8rem;
            font-size: 1.1rem;
        }

        & > a.router-link-exact-active {
            border-top: 3px solid $color-primary;
            font-weight: bold;
            color: $color-secondary;
        }
    }

    
}

.navigation__checkbox:checked + .navigation__button .navigation__icon {
    background-color: transparent;
}

//animation for top of burger menu icon when clicked
.navigation__checkbox:checked + .navigation__button .navigation__icon::before {
    top: 0;
    transform: rotate(135deg) !important;
}

//animation for bottom of burger menu icon when clicked
.navigation__checkbox:checked + .navigation__button .navigation__icon::after {
    top: 0;
    transform: rotate(-135deg) !important;
}
</style>
