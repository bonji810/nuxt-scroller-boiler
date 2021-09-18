<template>
<div class="smooth-scroll">
  <div class="description panel blue">
    <div><h1>Locomotive Scroll + ScrollTrigger</h1>
      <p>Demonstrates how ScrollTrigger can be used with a smooth scrolling library like Locomotive Scroll, including scrubbing and pinning.</p>
      <div class="scroll-down">Scroll down<div class="arrow"></div></div>
    </div>
  </div>


  <section class="panel red">
    <p><span class="line line-1"></span>This line's animation will begin when it enters the viewport and finish when its top edge hits the top of the viewport, staying perfectly in sync with the scrollbar because it has <code>scrub:&nbsp;true</code></p>
  </section>


  <section class="panel orange">
    <p><span class="line line-2"></span>This orange panel gets pinned when its top edge hits the top of the viewport, then the line's animation is linked with the scroll position until it has traveled 100% of the viewport's height (<code>end: "+=100%"</code>), then the orange panel is unpinned and normal scrolling resumes. Padding is added automatically to push the rest of the content down so that it catches up with the scroll when it unpins. You can set <code>pinSpacing: false</code> to prevent that if you prefer.</p>
  </section>


  <section class="panel purple">
    <p><span class="line line-3"></span>This panel gets pinned in a similar way, and has a more involved animation that's wrapped in a timeline, fading the background color and animating the transforms of the paragraph in addition to the line, all synced with the scroll position perfectly.</p>
  </section>



  <section class="panel gray">
    DONE!
  </section>
</div>
</template>

<script>
import Vue from 'vue'
import { gsap } from 'gsap/dist/gsap'
import { ScrollTrigger } from 'gsap/dist/ScrollTrigger'
gsap.registerPlugin(ScrollTrigger);
export default {
    mounted() {
        Vue.nextTick(() => {
          /* eslint-disable */
          const locoScroll = new this.LocomotiveScroll({
              el: document.querySelector(".smooth-scroll"),
              smooth: true
          });
          locoScroll.on("scroll", ScrollTrigger.update);
          ScrollTrigger.scrollerProxy(".smooth-scroll", {
          scrollTop(value) {
              return arguments.length ? locoScroll.scrollTo(value, 0, 0) : locoScroll.scroll.instance.scroll.y;
          },
          getBoundingClientRect() {
              return {top: 0, left: 0, width: window.innerWidth, height: window.innerHeight};
          },
          pinType: document.querySelector(".smooth-scroll").style.transform ? "transform" : "fixed"
          });
          
          ScrollTrigger.addEventListener("refresh", () => locoScroll.update());
          ScrollTrigger.refresh();
  
          this.animation()
        })
    },
    methods: {
      animation() {
        gsap.from(".line-1", {
        scrollTrigger: {
            trigger: ".line-1",
            scroller: ".smooth-scroll",
            scrub: true,
            start: "top bottom",
            end: "top top"
        },
        scaleX: 0,
        transformOrigin: "left center", 
        ease: "none"
        });
        // --- ORANGE PANEL ---
        gsap.from(".line-2", {
        scrollTrigger: {
            trigger: ".orange",
            scroller: ".smooth-scroll",
            scrub: true,
            pin: true,
            start: "top top",
            end: "+=100%"
        },
        scaleX: 0, 
        transformOrigin: "left center", 
        ease: "none"
        });
        // --- PURPLE/GREEN PANEL ---
        const tl = gsap.timeline({
            scrollTrigger: {
            trigger: ".purple",
            scroller: ".smooth-scroll",
            scrub: true,
            pin: true,
            start: "top top",
            end: "+=100%"
            }
        });
        tl.from(".purple p", {scale: 0.3, rotation:45, autoAlpha: 0, ease: "power2"})
        .from(".line-3", {scaleX: 0, transformOrigin: "left center", ease: "none"}, 0)
        .to(".purple", {backgroundColor: "#28a92b"}, 0);
      }
    }
}
</script>

