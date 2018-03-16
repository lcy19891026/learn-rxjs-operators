# Progress Bar

_By [@barryrowe](https://twitter.com/barryrowe)_

This recipe demonstrates the creation of an animated progress bar, simulating
the management of multiple requests, and updating overall progress as each
completes.

<div class="ua-ad"><a href="https://ultimateangular.com/?ref=76683_kee7y7vk"><img src="https://ultimateangular.com/assets/img/banners/ua-leader.svg"></a></div>

### Example Code

(
[StackBlitz](https://stackblitz.com/edit/rxjs-5-progress-bar-x33rrw?file=index.ts)
)

```js
import { Observable } from 'rxjs/Observable';
import { of } from 'rxjs/observable/of';
import { empty } from 'rxjs/observable/empty';
import { fromEvent } from 'rxjs/observable/fromEvent';
import { from } from 'rxjs/observable/from';
import {
  delay,
  switchMapTo,
  concatAll,
  count,
  scan,
  withLatestFrom,
  share
} from 'rxjs/operators';

const requestOne = of('first').pipe(delay(500));
const requestTwo = of('second').pipe(delay(800));
const requestThree = of('third').pipe(delay(1100));
const requestFour = of('fourth').pipe(delay(1400));
const requestFive = of('fifth').pipe(delay(1700));

const loadButton = document.getElementById('load');
const progressBar = document.getElementById('progress');
const content = document.getElementById('data');

// update progress bar as requests complete
const updateProgress = progressRatio => {
  console.log('Progress Ratio: ', progressRatio);
  progressBar.style.width = 100 * progressRatio + '%';
  if (progressRatio === 1) {
    progressBar.className += ' finished';
  } else {
    progressBar.className = progressBar.className.replace(' finished', '');
  }
};
// simple helper to log updates
const updateContent = newContent => {
  content.innerHTML += newContent;
};

const displayData = data => {
  updateContent(`<div class="content-item">${data}</div>`);
  console.log('ALL THE RESULTS: ', data);
};

// simulate 5 seperate requests that complete at variable length
const observables: Array<Observable<string>> = [
  requestOne,
  requestTwo,
  requestThree,
  requestFour,
  requestFive
];

const array$ = from(observables);
const requests$ = array$.pipe(concatAll());
const clicks$ = fromEvent(loadButton, 'click');

const progress$ = clicks$.pipe(switchMapTo(requests$), share());

const count$ = array$.pipe(count());

const ratio$ = progress$.pipe(
  scan(current => current + 1, 0),
  withLatestFrom(count$, (current, count) => current / count)
);

clicks$.pipe(switchMapTo(ratio$)).subscribe(updateProgress);

progress$.subscribe(displayData);
```

##### html

```html
<div class="progress-container">
  <div class="progress" id="progress"></div>
</div>

<button id="load">
Load Data
</button>

<div id="data">

</div>
```

_Thanks to [@johnlinquist](https://twitter.com/johnlindquist) for the additional
help with example!_

### Operators Used

* [concatAll](../operators/transformation/concatall.md)
* [delay](../operators/utility/delay.md)
* [fromEvent](../operators/creation/fromevent.md)
* [from](../operators/creation/from.md)
* [scan](../operators/transformation/scan.md)
* [share](../operators/multicasting/share.md)
* [switchMap](../operators/transformation/switchmap.md)
* [withLatestFrom](../operators/transformation/withlatestfrom.md)