# Documentação Técnica do Aplicativo Angular

## Estrutura do Aplicativo

O aplicativo é dividido em duas telas principais:

1. **ReportComponent**: Tela de entrada onde o usuário informa a data inicial e a data final usando o Angular Material Date Picker.
2. **DashboardComponent**: Tela que exibe um dashboard com informações baseadas no intervalo de datas fornecido.

## Componentes

### ReportComponent

### Descrição

Este componente permite ao usuário inserir uma data inicial e uma data final utilizando o Angular Material Date Picker. Ele é responsável por coletar os dados de entrada e enviá-los para o componente de dashboard.

### Inputs

- **startDate**: `string` - A data inicial do período.
- **endDate**: `string` - A data final do período.

### Outputs

- **onSubmit**: `EventEmitter<{startDate: string, endDate: string}>` - Evento emitido quando o usuário submete as datas.

### Código do Componente

```tsx
import { Component, EventEmitter, Output } from '@angular/core';
import { FormControl } from '@angular/forms';

@Component({
  selector: 'app-report',
  templateUrl: './report.component.html',
  styleUrls: ['./report.component.css']
})
export class ReportComponent {
  startDate = new FormControl();
  endDate = new FormControl();

  @Output() onSubmit = new EventEmitter<{ startDate: string, endDate: string }>();

  submitDates() {
    if (this.startDate.value && this.endDate.value) {
      this.onSubmit.emit({ startDate: this.startDate.value.toISOString(), endDate: this.endDate.value.toISOString() });
    }
  }
}
```

### Template do Componente

```html
<div>
  <mat-form-field appearance="fill">
    <mat-label>Data Inicial</mat-label>
    <input matInput [matDatepicker]="startPicker" [formControl]="startDate">
    <mat-datepicker-toggle matSuffix [for]="startPicker"></mat-datepicker-toggle>
    <mat-datepicker #startPicker></mat-datepicker>
  </mat-form-field>
</div>

<div>
  <mat-form-field appearance="fill">
    <mat-label>Data Final</mat-label>
    <input matInput [matDatepicker]="endPicker" [formControl]="endDate">
    <mat-datepicker-toggle matSuffix [for]="endPicker"></mat-datepicker-toggle>
    <mat-datepicker #endPicker></mat-datepicker>
  </mat-form-field>
</div>

<button mat-raised-button color="primary" (click)="submitDates()">Submeter</button>

```

### DashboardComponent

### Descrição

Este componente exibe um dashboard com informações baseadas nas datas fornecidas pelo `ReportComponent`.

### Inputs

- **startDate**: `string` - A data inicial do período.
- **endDate**: `string` - A data final do período.

### Outputs

- **onDataLoad**: `EventEmitter<any>` - Evento emitido quando os dados do dashboard são carregados.

### Código do Componente

```tsx
import { Component, Input, OnChanges, SimpleChanges, EventEmitter, Output } from '@angular/core';

@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.component.html',
  styleUrls: ['./dashboard.component.css']
})
export class DashboardComponent implements OnChanges {
  @Input() startDate!: string;
  @Input() endDate!: string;
  @Output() onDataLoad = new EventEmitter<any>();

  data: any;

  ngOnChanges(changes: SimpleChanges) {
    if (changes.startDate || changes.endDate) {
      this.loadData();
    }
  }

  loadData() {
    // Lógica para carregar os dados do dashboard com base em startDate e endDate
    // Exemplo fictício:
    this.data = {
      totalSales: 1000,
      totalOrders: 100,
      bestSellingProduct: 'Produto X'
    };
    this.onDataLoad.emit(this.data);
  }
}
```

## Comunicação entre Componentes

A comunicação entre os componentes `ReportComponent` e `DashboardComponent` é feita através da emissão de eventos e vinculação de propriedades (property binding). Quando o usuário submete as datas no `ReportComponent`, o evento `onSubmit` é emitido com as datas, que são então passadas para o `DashboardComponent`.

## Código de Exemplo

Aqui está um exemplo de como usar os componentes no template principal do aplicativo:

```html
<app-report (onSubmit)="handleDateSelection($event)"></app-report>
<app-dashboard [startDate]="selectedDates?.startDate" [endDate]="selectedDates?.endDate"></app-dashboard>

```

E no arquivo TypeScript do componente principal:

```tsx
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  selectedDates: { startDate: string, endDate: string } | null = null;

  handleDateSelection(dates: { startDate: string, endDate: string }) {
    this.selectedDates = dates;
  }
}

```