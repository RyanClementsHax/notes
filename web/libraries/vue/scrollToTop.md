# Scroll To Top

- a lot taken from [this](https://css-tricks.com/how-to-make-an-unobtrusive-scroll-to-top-button/) css tricks post
- something like this can also be done [using vue router](https://router.vuejs.org/guide/advanced/scroll-behavior.html)
- you can also use an [observability library](https://github.com/Akryum/vue-observe-visibility) to do what we are doing here

## How to implement
1. create the html for the button you want to conditionally show when another element (say a header) goes out of .... vue ....... (im so funny)
    ```html
        <h1 ref="header">Header</h1>
        <!-- some long content in between -->
        <b-button
            v-if="showScrollToTopBtn"
            variant="primary"
            class="scroll-to-top-btn"
            @click="scrollToTop"
        >
            <b-icon icon="arrow-up-short" />
        </b-button>
    ```
2. set up an observer that will set some boolean whenever the thing being observed goes out of view
    ```js
    data() {
        return {
            showScrollToTopBtn: false,
            scrollObserver: new IntersectionObserver(entries =>
                entries.forEach(
                    entry => (this.showScrollToTopBtn = !entry.isIntersecting)
                )
            )
        }
    }
    ```
3. start the observer on mount and disconnect before destroying
    ```js
    mounted() {
        this.scrollObserver.observe(this.$refs['header'])
    },
    beforeDestroy() {
        this.scrollObserver.disconnect()
    },
    ```
4. create a method for the scroll to top button to call
    ```js
    methods: {
        scrollToTop() {
            this.$refs['header'].scrollIntoView({
                behavior: 'smooth'
            })
        }
    }
    ```
5. create some css to ease the showing of the button
    ```css
    .scroll-to-top-btn {
        position: fixed;
        bottom: 15px;
        right: 15px;
        z-index: 99;
        opacity: 0;
        transform: translateY(100px);
        transition: all 0.5s ease;
    }

    .show-btn {
        opacity: 1;
        transform: translateY(0);
    }
    ```
6. have fun!

## Using vue's transition support for fading the button in and out
- read up [here](https://vuejs.org/v2/guide/transitions.html) for how vue's transition classes work


1. change the html to use the transition tag and give it a name to reference in the css (in this case it is `fade`)
    ```html
    <transition name="fade">
        <b-button
        v-if="showScrollToTopBtn"
        variant="primary"
        class="scroll-to-top-btn"
        @click="scrollToTop"
        >
        <b-icon icon="arrow-up-short" />
        </b-button>
    </transition>
    ```

2. use the classes in your css
    ```css
    .fade-enter-active,
    .fade-leave-active {
        transition: all 0.5s ease;
    }
    .fade-enter,
    .fade-leave-to {
        opacity: 0;
        transform: translateY(100px);
    }

    .scroll-to-top-btn {
        position: fixed;
        bottom: 15px;
        right: 15px;
        z-index: 99;
    }
    ```

## Misc
- it should also be noted that you can use the raw api on the window to scroll to a specific location if you don't need to scroll to a specific element
    ```js
    // scrolls to the top smoothly
    window.scrollTo({
        top: 0,
        left: 0,
        behavior: 'smooth'
    })
    ```