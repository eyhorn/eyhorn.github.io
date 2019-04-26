---
layout: post
title:  "Swap DOM elements with animation using vanilla Javascript"
date:   2019-04-26 11:28:00 +1100
categories: javascript html css chrome
---
My son has been working on a school project involving demonstration of swapping elements on the screen. When he asked for my help my initial reaction was that it should be done using React. Upon reflection, I have discarded this idea because:

* my son's project is hosted on [GitHub Pages](https://pages.github.com/)
* he would have to learn React
* all the code he has written is in vanilla js

All the modern browsers are supporting extensive DOM manipulation API and to swap DOM nodes only a few methods are required and making changes observable can be easily solved using CSS transitions.

Base on all these assumptions I have prepared [simple demo](https://github.com/eyhorn/swap-dom-nodes/blob/master/index.html) project that helped my son to get going. In the link above I've shared full demo but I will walk through some of it here.
To begin with we need user interaction elements and placeholder where we will render a list of numbers:

```html
<body>
    <div id="numbers"></div>
    <label>Delay</label> <select id="delay">
        <option value=0>No delays</option>
        <option value=100>0.1s</option>
        <option value=200>0.2s</option>
        <option value=300>0.3s</option>
        <option value=400>0.4s</option>
        <option value=500 selected>0.5s</option>
        <option value=600>0.6s</option>
        <option value=700>0.7s</option>
        <option value=800>0.8s</option>
        <option value=900>0.9s</option>
        <option value=1000>1s</option>
    </select>
    <button id="goButton" onclick="swap()">Go</button>
</body>
```

Some basics styles that will be used in the demo are provided below:

```css
.number {
    width: 100px;
}
.inspected-number {
    background-color: palegoldenrod;
}
```

Upon loading of DOM content we will initialize and render list of numbers:

```javascript
const numberOfElements = 20;
let numbers = [];
let numbersPlaceholder;
let delayContainer;

document.addEventListener("DOMContentLoaded", () => {
    init();
    renderNumbers();
});

function init() {
    numbersPlaceholder = document.getElementById('numbers');
    delayContainer = document.getElementById('delay');
    for(let i = 1; i <= numberOfElements; i++) {
        numbers.push(i);
    }
}

function renderNumbers() {
    for (const number of numbers) {
        let numberRepresentationContainer = document.createElement('div');
        numberRepresentationContainer.classList.add('number');
        numberRepresentationContainer.appendChild(document.createTextNode(number));
        numbersPlaceholder.appendChild(numberRepresentationContainer);
    }
}
```

Now, let's take a look at the actual swapping:

```javascript
async function swap() {
    let delay = delayContainer.selectedOptions[0].value;
    for(let i = 1; i < numberOfElements; i++) {
        numbersPlaceholder.children[i].classList.add('inspected-number');

        if(numbers[i] % 2 !== 0) {
            await sleep(delay);
            numbersPlaceholder.children[i].classList.remove('inspected-number');
            continue;
        }

        let rowHeight = numbersPlaceholder.children[i].clientHeight;
        let animationLength = delay;
        numbersPlaceholder.children[i].setAttribute('style', `transform: translate(0px, -${rowHeight}px); background-color: palegoldenrod; transition: background-color ${animationLength}ms, transform ${animationLength}ms`);
        numbersPlaceholder.children[i-1].setAttribute('style', `transform: translate(0px, ${rowHeight}px); background-color: orange; transition: background-color ${animationLength}ms, transform ${animationLength}ms`);

        await sleep(delay);
        [numbers[i-1], numbers[i]] = [numbers[i], numbers[i-1]];
        numbersPlaceholder.children[i].after(numbersPlaceholder.children[i-1]);

        numbersPlaceholder.children[i-1].classList.remove('inspected-number');
        numbersPlaceholder.children[i].removeAttribute("style");
        numbersPlaceholder.children[i-1].removeAttribute("style");
    }
}
```

When we are looping through the numbers we are assigning each div containing number a class `.inspected-number` to highlight the currently assessed number. Odd numbers are skipped but only after a delay. I will list the `sleep` function a little bit later. If a number is even when it will be swapped with the preceding number. The array of numbers and their DOM representation are kept in sync by swapping elements in both. Array elements are swapped using [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) and the DOM elements by using jQuery inspired DOM manipulation method `numbersPlaceholder.children[i].after(numbersPlaceholder.children[i-1]);`.
Before any elements are swapped we are allowing the animation to finish. Row movement is animated using `transform: translate()` and the background colour is also during animation length equal to delay selected by the user. All these transitions could have been simplified by using `translateY()` and specifying `transition: all ${animationLength}ms` but I decided to be a bit more verbose for the purposes of the demo.

The helper `sleep()` function resolves a promise after the specified timeout expires:

```javascript
function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}
```

This demo is far from perfect and it is not pretty but it should be useful to someone who is looking at swapping DOM elements in modern browsers.