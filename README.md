# Angular-Google-Charts

![CircleCI](https://img.shields.io/circleci/build/gh/FERNman/angular-google-charts) ![David](https://img.shields.io/david/FERNman/angular-google-charts) [![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org) ![npm](https://img.shields.io/npm/dm/angular-google-charts)

> A wrapper for the [Google Charts library](https://google-developers.appspot.com/chart/) written in Angular.

## Setup

### Installation

To use Angular-Google-Charts in your project, install the package with npm by calling

```bash
npm install angular-google-charts
```

This will add the package to your package.json and install the required dependencies.

### Importing

Import the `GoogleChartsModule` in your `app.module.ts`:

```typescript
import { GoogleChartsModule } from 'angular-google-charts';

@NgModule({
  ...
  imports: [
    ...
    GoogleChartsModule,
    ...
  ],
  ...
})
export class AppModule {}
```

This will allow you to use all of the features provided by this library.

#### Configuring

For some use cases, it might be necessary to use some different config options than the default values.

All config options for Angular Google Charts are provided through a config object, which
can be passed to the library by importing the `GoogleChartsModule` using its `forRoot` method.

```typescript
GoogleChartsModule.forRoot({ version: 'chart-version' }),
```

## Charts

The easiest way to create a chart is using the `GoogleChartComponent`.

```html
<google-chart></google-chart>
```

Using the component, it is possible to create every chart in the Google Charts library.
It has a few important input properties, which are explained below.

### Type

```html
<google-chart [type]="myType"></google-chart>
```

The type of chart you want to create. Must be of type `ChartType`.

To see examples for all chart types and more information, visit the [google chart gallery](https://google-developers.appspot.com/chart/interactive/docs/gallery).

### Data

```html
<google-chart [data]="myData"></google-chart>
```

The data property expects a two-dimensional array.
How the array should look like in detail depends on the chart type you want to create and what you want to display.

```typescript
myData = [
  ['London', 8136000],
  ['New York', 8538000],
  ['Paris', 2244000],
  ['Berlin', 3470000],
  ['Kairo', 19500000],
  ...
];
```

The data object can also include formatters for the given data. To use these, pass an object of type `{ v: any, f: string }` as the data values in the inner array. The property `v` should contain the real value, and the property `f` the formatted value.

Formatters can also be passed as a separate input property, see [Formatters](#formatters);

```typescript
myData = [
  ['London', {v: 8136000, f: '8,1360'}],
  ['New York', {v: 8538000, f: '8,530'}],
  ...
];
```

For further information, please see the official [documentation](https://google-developers.appspot.com/chart/interactive/docs/reference#arraytodatatable) on `ArrayToDataTable`, which is the function used internally.

### Columns

```html
<google-chart [columns]="chartColumns"></google-chart>
```

The `columns` property expects an `Array<string>` containing the names for each column of the chart data. The number of entries must match the length of the inner array passed in the `data` property.
Some charts don't require columns. Whether your chart requires it can be check in the official documentation.

```typescript
chartColumns = ['City', 'Inhabitants'];
```

### Title

```html
<google-chart [title]="myTitle"></google-chart>
```

The `title` property is optional and provided for convenience. It can also be included in the `options` property.

### Width

```html
<google-chart [width]="myWidth"></google-chart>
```

The `width` property is optional and allows to set the width of the chart. The number provided will be converted to a pixel value. The default is `undefined`, which makes the chart figure out its width by itself.
You can also set the width using CSS, which has the advantage of allowing `%` values instead of only pixels. For more information on that, see [dynamic resize](#dynamic-resize).

### Height

```html
<google-chart [height]="myHeight"></google-chart>
```

The `height` property is optional and allows to set the height of the chart. The number provided will be converted to a pixel value. The default is `undefined`, which makes the chart figure out its height by itself.
You can also set the height using CSS, which has the advantage of allowing `%` values instead of only pixels. For more information on that, see [dynamic resize](#dynamic-resize).

### Options

```html
<google-chart [options]="myOptions"></google-chart>
```

The `options` property is optional and allows to customize the chart to a great extent. How and what you can customize depends on the type of chart. For more information, please see the [google documentation](https://google-developers.appspot.com/chart/interactive/docs/customizing_charts).

```typescript
// example
myOptions = {
  colors: ['#e0440e', '#e6693e', '#ec8f6e', '#f3b49f', '#f6c7b6'],
  is3D: true
};
```

### Formatters

```html
<raw-chart [formatters]="myFormatters"></raw-chart>
```

The `formatter` property is optional and allows to format the chart data. It requires an array of objects containing a formatter and an index.

For more information and all formatter types, please refer to the [documentation](https://google-developers.appspot.com/chart/interactive/docs/reference#formatters).

```typescript
// Formats the column with the index 1 and 3 to Date(long)
myFormatters = [
  {
    formatter: new google.visualization.Dateformat({ formatType: 'long' }),
    colIndex: 1
  },
  {
    formatter: new google.visualization.Dateformat({ formatType: 'long' }),
    colIndex: 3
  }
];
```

_Note: When you get the error "google is not defined" whilst using the formatter in your component, you probably didn't load the google charts script. Please read the chapter on using the [ScriptLoaderService](#using-the-scriptloaderservice`)._

### Dynamic Resize

```html
<raw-chart [dynamicResize]="dynamicResize"></raw-chart>
```

The `dynamicResize` property is optional and makes your chart redraw every time the window is resized.
Defaults to `false` and should only be used when setting the width or height of the chart to a percentage value.
Otherwise, the chart gets redrawn unnecessary and therefore slows down the site.

### Styling

```html
<raw-chart style="width: 100%;"></raw-chart>
```

Most CSS properties should work exactly as you would expect them to.
If you want to have the chart full-width for example, set the width to `100%`.

## Events

The `GoogleChartComponent` provides bindings for the most common Google Chart events.

### Ready

The [`ready` event](https://developers.google.com/chart/interactive/docs/events#the-ready-event) is emitted as soon as the chart got drawn and after every subsequent redraw.

```html
<google-chart (ready)="onReady($event)"></google-chart>
```

The event is of type `ChartReadyEvent`.

### Error

The [`error` event](https://developers.google.com/chart/interactive/docs/events#the-error-event) is emitted when an internal error occurs. However, since the newer versions of google-charts, most errors are displayed in the chart HTML as well. It can be bound to like this:

```html
<google-chart (error)="onError($event)"></google-chart>
```

The event is of type `ChartErrorEvent`.

### Select

The [`select` event](https://developers.google.com/chart/interactive/docs/events#the-select-event) is emitted when an element in the chart gets selected.

```html
<google-chart (select)="onSelect($event)"></google-chart>
```

The event of type `ChartSelectionChangedEvent` containing an array of selected values.

### Mouseover

The `mouseover` event fires when the mouse hovers over one of the charts elements (i. e. a bar in a bar chart or a segment in a pie chart).

```html
<google-chart (mouseover)="OnMouseOver($event)"></google-chart>
```

The event is of type `ChartMouseOverEvent`, where `column` is the index of the hovered column and `row` is the index of the hovered row.

### Mouseleave

The `mouseleave` event fires when the mouse stops hovering one of the charts elements (i. e. a bar in a bar chart or a segment in a pie chart).

```html
<google-chart (mouseleave)="onMouseLeave($event)"></google-chart>
```

The event is of type `ChartMouseLeaveEvent`, where `column` is the index of the no-longer hovered column and `row` is the index of the no-longer hovered row.

## Advanced

### Accessing the chart wrapper directly

I case you don't need any of the special features the `GoogleChartsComponent` provides, the `RawChartComponent` can be used.
It is a direct wrapper of the [`ChartWrapper`](https://developers.google.com/chart/interactive/docs/reference#chartwrapper-class)..

```html
<raw-chart [chartData]="myChartdata"></raw-chart>
```

The `RawChartComponent` should be used if you need more fine-grained control over the data you are providing or you want to use
the query feature that Google Charts provides, which is not supported using the `GoogleChartComponent`.

### Using the `ScriptLoaderService`

If a specific chart is created a lot in your application, you may want to create custom components.

When doing so, you need to load the chart packages by yourself.
The `ScriptLoaderService` provides a few methods helping with this.

```typescript
class MyComponent {
  private readonly chartPackage = getPackageForChart(ChartType.BarChart);

  @ViewChild('container', { read: ElementRef })
  private containerEl: ElementRef<HTMLElement>;

  constructor(private loaderService: ScriptLoaderService) {}

  ngOnInit() {
    this.loaderService.loadChartPackages(this.chartPackage).subscribe(() => {
      // Start creating your chart now
      const char = new google.visualization.BarChart(this.containerEl.nativeElement);
    });
  }
}
```

If you don't need a specific chart package but just want to access the `google.charts` namespace,
you can use the method `ScriptLoaderService.loadGoogleCharts()`.
It will load the script into the current browser session.

```typescript
ngOnInit() {
  this.loaderService.loadGoogleCharts().subscribe(() => {
    console.log(this.loaderService.isGoogleChartsAvailable()); // true

    // You can now use `google.charts`
    google.charts.load();
  });
}
```

### Preloading the Google Charts script

If the existence of charts is crucial to your application, you may want to decrease the time it takes until the first chart becomes visible.
This can be achieved by loading the Google Charts script concurrently with the rest of the application.
In the playground application, this reduces the time until the first chart appears by roughly 20%, which means for
example about 4 seconds when using the "Slow 3G" profile in Chrome DevTools.

To achieve this, two scripts have to be added to the `index.html` file in your apps' root folder.
The first one loads the generic Google Charts script, the second one the version-specific parts of the library needed to load charts.

In the code below, `<chart_version>` has to be replaced with the **exact** of the Google Charts library that you want to use and must match the version you use when importing the `GoogleChartsModule`.

The only exception to this is version `46`. All minor versions of Google Charts v46 require the loader to be of version `46.2`.

```html
<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js" async></script>
<script type="text/javascript" src="https://www.gstatic.com/charts/<chart_version>/loader.js" async></script>
```

Please note that this can increase the time it takes until Angular is fully loaded.
I suggest doing some benchmarks with your specific application before deploying this to production.

## License

This project is provided under the [MIT license](https://github.com/FERNman/angular-google-charts/blob/master/LICENSE.md).
