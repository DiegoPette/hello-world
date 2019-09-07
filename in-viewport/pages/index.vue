<template>
    <div class="bodypage">
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item">
            <div class="elem" ref="elem" id="elem">State: {{elemState}} </div>
        </div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>

        <div class="terminal" v-if="elem">
            <p>pageHeight: {{pageHeight}}</p>
            <p>viewport: {{viewport}}</p>
            <p>elem Top: {{elem.top}}</p>
            <p>elem Bottom: {{elem.bottom}}</p>
            <p>scrollY: {{scrollY}}</p>
            <p>viewTopMargin: {{viewportTopMargin}}</p>
            <p>viewBottomMargin: {{viewportBottomMargin}}</p>
            <p>elementPos: {{elementPos}}</p>
            <label for="viewTopMargin"> Viewport Top Margin </label>
            <input v-model="viewportTopMargin" name="viewTopMargin" id="viewTopMargin" />
            <br/>
            <label for="viewBottomMargin"> Viewport Top Margin </label>
            <input v-model="viewportBottomMargin" name="viewBottomMargin" id="viewBottomMargin" />
        </div>
        <div class="margin topMargin" :style="{'top': viewportTopMargin+'px'}"></div>
        <div class="margin bottomMargin" :style="{'bottom': viewportBottomMargin+'px'}"></div>
    </div>
</template>

<script>
import Logo from '~/components/Logo.vue'
    
export default {
    data(){
        return {
            pageHeight: null,
            elem: null,
            elementPos: null,
            viewport: null,
            refname: 'elem',
            scrollY: null,
            viewportBottomMargin: 0,
            viewportTopMargin: 0,
        }
    },
    created () {
        if (process.browser) { 
            window.addEventListener('scroll', this.eventHandler);
            window.addEventListener('resize', this.eventHandler);
        }
    },
    destroyed () {
        if (process.browser) { 
            window.removeEventListener('scroll', this.eventHandler);
            window.removeEventListener('resize', this.eventHandler);
        }
    },
    mounted(){
        this.updatePositions();
    },
    methods: {
        eventHandler() {
            this.updatePositions();
        },
        updatePositions(){
            this.pageHeight = +this.getPageHeight();
            this.elem = this.getElemClientRect(this.refname);
            this.viewport = +this.getViewportHeight();
            this.scrollY = +this.getScrollY();
            this.elementPos = this.checkPosition();
        },
        checkPosition(){
            var position;
            if(this.elem.top > (this.viewport - this.viewportBottomMargin)){  // 1
                position = 'below-viewport';
            } else if(this.elem.top <= (this.viewport - this.viewportBottomMargin) && this.elem.top > (0 + this.viewportTopMargin) && this.elem.bottom > (this.viewport - this.viewportBottomMargin) ){ // 2
                position = 'partially-in-viewport-below';
            } else if(this.elem.top < (0 + this.viewportTopMargin) && this.elem.bottom > (this.viewport - this.viewportBottomMargin) ) { // 3
                position = 'partailly-in-viewport-both';
            } else if(this.elem.top < (this.viewport - this.viewportBottomMargin) && this.elem.top >= (0 + this.viewportTopMargin) && this.elem.bottom <= (this.viewport - this.viewportBottomMargin)){ // 4
                position = 'fully-in-viewport';
            } else if(this.elem.top < (0 + this.viewportTopMargin) && this.elem.bottom < (this.viewport - this.viewportBottomMargin) && this.elem.bottom > (0 + this.viewportTopMargin)){ // 5
                position = 'partially-in-viewport-top';
            } else if(this.elem.top < (0 + this.viewportTopMargin) && this.elem.bottom <= (0 + this.viewportTopMargin)){ // 6
                position = 'above-viewport';
            }
            return position;
        },
        getScrollY(){
            var scrollY;
            if(window.scrollY){
                scrollY = window.scrollY;
            } else {
                scrollY = document.documentElement.scrollTop;
            }
            return scrollY;
        },
        getPageHeight(){
            var body = document.body, html = document.documentElement;
            return Math.max( body.scrollHeight, body.offsetHeight,html.clientHeight, html.scrollHeight, html.offsetHeight);
        },
        getElemClientRect(ref){
            return JSON.parse(JSON.stringify(this.$refs[ref].getBoundingClientRect()));
        },
        getViewportHeight(){
            var viewportheight;
            if (typeof window.innerHeight != 'undefined'){
                viewportheight = window.innerHeight
            } else if (typeof document.documentElement != 'undefined' && typeof document.documentElement.clientHeight != 'undefined' && document.documentElement.clientHeight != 0){
                viewportheight = document.documentElement.clientHeight
            }else {
                viewportheight = document.getElementsByTagName('body')[0].clientHeight
            }
            return viewportheight;
        }
    }
}
</script>
