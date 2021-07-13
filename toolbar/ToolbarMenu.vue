<template>
    <div class="toolbar-menu" id='toolbar-menu'>
        <input
            type="checkbox"
            id="navigation-toggle"
        />
        <div class="title">
            <label class='open-icon'></label>
            <slot name="title" id="title-slot"></slot>
        </div>
        <div class='expanding-container'>
            <div class="inner-container">
                <slot name="items" id="items-slot"></slot>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: "toolbar-menu",
    data() {
        return {
            observer: {
                type: Object
            },
            menuVisable:  {
                type: Boolean
            }
        };
    },
    mounted() {
        this._addTransitionListener();
        this._addChildListeners();
        this.menuVisable = false; 
        this._closeMenu(); //bypassed in desktop mode; we need to call this manually because setting the property above wont trigger it
    },

    watch: {
        menuVisable( visible ){
            if( window.innerWidth > 1150 ) { //Desktop bypass; dont do anything if in desktop mode
                return;
            }
            if( visible ) {
                this._openMenu();
            } else {
                this._closeMenu();
            }
        }
    },  

    methods: {
        _addTransitionListener() {
            this.$el.querySelector( '.inner-container' ).addEventListener( 'nested-transition', ( e ) => {
                //only nested
                //when the transition starts change our height based on what the child is doing: expanding/contracting
                let height = this.$el.querySelector( '.inner-container' ).offsetHeight;
                if( e.detail.visible ) {
                    height += e.detail.height;
                } else {
                    height -= e.detail.height
                }
                this._transition( height );
            } );
        },

        _addChildListeners() {
            let children = this.$el.querySelector( '.inner-container' ).children;
            if( children && children.length > 0 ) { //only if you have children
                this.$el.querySelector( '.title' ).addEventListener( "click", ( e ) => {
                    this._changeToolbarMenuView( this.menuVisable = !this.menuVisable ); //show/hide the menu
                    if( window.innerWidth > 1150 ) { //dont transition in destop mode
                        return;
                    }
                    this._notifyTransitioning( this.$el.querySelector( '.inner-container' ).offsetHeight, this.menuVisable ); //notify that the menu changes size
                } );
                for( let i = 0; i < children.length; i++ ) {
                    let child = children[i];
                    if( child.id !== 'toolbar-menu' ) { //only close fullscreen when not a nested menu ie. a link
                        child.addEventListener( "click", ( e ) => {
                            this._closeFullscreenMenu();
                        } );
                    }
                }
            }
        },

        _openMenu() {            
            this.$el.setAttribute( 'open', true );
            let height = this.$el.querySelector( '.inner-container' ).offsetHeight;
            this._transition( height );
        },

        _closeMenu() {
            this.$el.setAttribute( 'open', false );
            this._transition( 0 );
        },

        _closeFullscreenMenu() {
            this.$el.dispatchEvent( new CustomEvent( 'close-fullscreen-toolbar', {bubbles: true} ) );
        },

        _changeToolbarMenuView( value ) {
            this.$el.querySelector( '#navigation-toggle' ).checked = value;
        },

        _transition( height ) {
            let container = this.$el.querySelector( '.expanding-container' );
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

        _notifyTransitioning( height, visible ) {
            this.$el.dispatchEvent( new CustomEvent( 'nested-transition', {bubbles: true, detail: { 'height': height, 'visible': visible } } ) );
        }
    }
};
</script>
<style lang="scss" scoped>
//hide checkbox, only used for menu display/hide in mobile view
#navigation-toggle {
    display: none;
}

//click on a toolbar menu to show/hide the element. Makes us of a hidden checkbox for functionality
//only when no hover available

.toolbar-menu {
    cursor: pointer;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    overflow: hidden;

    & > .title {
        display: flex;
        font-weight: 700;

    }

    & > .expanding-container {
        justify-content: center;
        transition: all .2s ease-in-out;
        
        
        & > .inner-container {
            display: flex;
            flex-direction: column;
            justify-content: center;
            text-align: center;
            background: $color-white;
            width: 21rem;
            z-index: -1;
            padding-bottom: .5rem;

            
            -webkit-box-shadow: 0px 2px 15px 2px rgba( 29, 28, 28, .15 ); 
            box-shadow: 0px 2px 15px 2px rgba( 29, 28, 28, .15 );

            @include respond-to( 'tab-land' ){
                -webkit-box-shadow: none; 
                box-shadow: none;
            }

            & > a {
                font-size: .95rem;
                font-weight: 400;
            }

            & > a.router-link-exact-active {
                font-weight: 700;
                color: $color-secondary;
                border-top: 3px solid $color-primary;
            }

            & > a {
                border-top: 3px solid $transparent;
            }

            & > a:hover {
                border-top: 3px solid $color-primary;
            }

            //nested
            & > .toolbar-menu > .title {
                font-size: 1rem;
                margin: .5rem 0;
                padding: 0 .5rem;
            }
        }
    }    
}

//hover funcionality if supported on > tab-port
@media ( hover: hover ) AND ( min-width: 1150px ) {
    // hover functionality for nested
    .toolbar-menu  {
        position: relative;        

        &:hover {
            overflow: visible;
        }

        & > .expanding-container {            
            position: absolute;
            bottom: 0;

            & > .inner-container {
                position: absolute;
                transform: translateX( -50% ) !important;

                //nested
                & > .toolbar-menu {
                    
                    & > .expanding-container {
                        display: none;
                    }
                }

                & > .toolbar-menu:hover > .expanding-container {
                    display: flex;
                    top: 0;
                    
                    & > .inner-container {
                        transform: translateX( 100% ) !important;
                    }
                }
            }
        }

        &:last-child > .expanding-container > .inner-container > .toolbar-menu:hover > .expanding-container > .inner-container {
            transform: translateX( -100% ) !important;
        }        
    }
}

//phone/tablet mode
@media ( max-width: 1150px ) {
    .toolbar-menu {

        &:hover {
            overflow: hidden;
        }

        & > .expanding-container {
            position: relative;

            & > .inner-container {
                transform: translateX( 0 ) !important;
            }
        }

        & > .title > .open-icon {
            margin-right: .2rem;

            &::after {
                content: "+";
            }
        }

        &[open='true'] > .title > .open-icon::after {
            content: "-";
        }
    }
}

@media ( hover: none ) AND ( min-width: 1150px ) {
    // hover functionality for nested
    .toolbar-menu  {
        position: relative;
        overflow: visible;

        & > .expanding-container {
            position: absolute;
            bottom: 0;
            overflow: hidden;

            & > .inner-container {
                position: absolute;
                transform: translateX( -50% ) !important;

                //nested
                & > .toolbar-menu {
                    
                    & > .expanding-container {
                        top: 0;
                        display: none;

                        & > .inner-container {
                            transform: translateX( 50% ) !important;
                        }
                    }
                }
            }
        }
        
        & > #navigation-toggle:checked ~ .expanding-container {
            overflow: visible;
            display: block;
        }

        &:last-child > .expanding-container > .inner-container > .toolbar-menu > .expanding-container > .inner-container {
            transform: translateX( -150% ) !important;
        }
    }
}

</style>
