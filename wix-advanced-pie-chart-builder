// wix-advanced-pie-chart-builder.js

class AdvancedPieChartBuilder extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this.chartInstance = null;
    this.chartData = {
      labels: [],
      datasets: [{
        data: [],
        backgroundColor: [],
        hoverBackgroundColor: [],
        borderWidth: 1
      }]
    };
    this.chartOptions = {
      responsive: true,
      maintainAspectRatio: false,
      legend: {
        position: 'right',
        labels: {
          fontSize: 12,
          fontFamily: 'Arial'
        }
      },
      title: {
        display: true,
        text: 'My Pie Chart',
        fontSize: 18,
        fontFamily: 'Arial'
      },
      animation: {
        animateScale: true,
        animateRotate: true
      }
    };
    
    this.defaultColors = [
      '#FF6384', '#36A2EB', '#FFCE56', '#4BC0C0', '#9966FF',
      '#FF9F40', '#FFCD56', '#C9CBCF', '#7BC8A4', '#E6B0AA'
    ];
    this.isEditorVisible = true;
  }

  connectedCallback() {
    this.render();
    this.setupEventListeners();
  }

  render() {
    const styles = `
      :host {
        display: block;
        font-family: Arial, sans-serif;
        color: #333;
        --primary-color: #4a90e2;
        --secondary-color: #f5f5f5;
        --border-color: #ddd;
        --success-color: #28a745;
        --tab-hover-color: #eaf2fd;
      }

      .container {
        width: 100%;
        max-width: 1200px;
        margin: 0 auto;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        border-radius: 8px;
        overflow: hidden;
      }

      .editor {
        display: ${this.isEditorVisible ? 'block' : 'none'};
      }

      .tabs {
        display: flex;
        background-color: var(--secondary-color);
        border-bottom: 1px solid var(--border-color);
      }

      .tab {
        padding: 12px 20px;
        cursor: pointer;
        transition: background-color 0.3s;
        font-weight: bold;
      }

      .tab:hover {
        background-color: var(--tab-hover-color);
      }

      .tab.active {
        background-color: white;
        border-bottom: 3px solid var(--primary-color);
      }

      .tab-content {
        display: none;
        padding: 20px;
        background-color: white;
      }

      .tab-content.active {
        display: block;
      }

      .form-group {
        margin-bottom: 15px;
      }

      label {
        display: block;
        margin-bottom: 5px;
        font-weight: bold;
      }

      input, select, textarea {
        width: 100%;
        padding: 8px 12px;
        border: 1px solid var(--border-color);
        border-radius: 4px;
        font-size: 14px;
      }

      button {
        background-color: var(--primary-color);
        color: white;
        border: none;
        padding: 10px 15px;
        border-radius: 4px;
        cursor: pointer;
        font-size: 14px;
        transition: background-color 0.3s;
      }

      button:hover {
        background-color: #3a7bbf;
      }

      .btn-success {
        background-color: var(--success-color);
      }

      .btn-success:hover {
        background-color: #218838;
      }

      .btn-secondary {
        background-color: #6c757d;
      }

      .btn-secondary:hover {
        background-color: #5a6268;
      }

      .data-row {
        display: flex;
        margin-bottom: 10px;
      }

      .data-row input {
        margin-right: 10px;
      }
      
      .data-row .color-picker {
        width: 80px;
      }

      .data-row .delete-btn {
        background-color: #dc3545;
        color: white;
        width: 40px;
        height: 40px;
        display: flex;
        align-items: center;
        justify-content: center;
        border-radius: 4px;
        cursor: pointer;
        margin-left: 10px;
      }

      .actions {
        display: flex;
        justify-content: space-between;
        margin-top: 20px;
        padding: 15px;
        background-color: var(--secondary-color);
        border-top: 1px solid var(--border-color);
      }

      .chart-container {
        height: 400px;
        width: 100%;
        position: relative;
        margin: 20px 0;
      }

      .edit-button {
        position: absolute;
        top: 10px;
        right: 10px;
        z-index: 10;
        display: ${this.isEditorVisible ? 'none' : 'block'};
      }

      .color-grid {
        display: grid;
        grid-template-columns: repeat(5, 1fr);
        gap: 5px;
        margin-top: 10px;
      }

      .color-item {
        height: 25px;
        border-radius: 4px;
        cursor: pointer;
      }

      .import-export {
        display: flex;
        gap: 10px;
        margin-top: 15px;
      }
    `;

    this.shadowRoot.innerHTML = `
      <style>${styles}</style>
      <div class="container">
        <button class="edit-button">Edit Chart</button>
        
        <div class="editor">
          <div class="tabs">
            <div class="tab active" data-tab="data">Data Input</div>
            <div class="tab" data-tab="appearance">Appearance</div>
          </div>
          
          <div class="tab-content active" data-tab="data">
            <div class="form-group">
              <label for="chart-title">Chart Title</label>
              <input type="text" id="chart-title" value="My Pie Chart">
            </div>
            
            <h3>Chart Data</h3>
            <div id="data-entries">
              <!-- Data rows will be added here -->
            </div>
            
            <div class="actions-row">
              <button id="add-data-row">Add Item</button>
              <button id="bulk-add">Bulk Add</button>
            </div>
            
            <div class="import-export">
              <button id="import-data">Import Data</button>
              <button id="export-data">Export Data</button>
            </div>
            
            <div id="bulk-add-container" style="display: none; margin-top: 15px;">
              <label for="bulk-data">Enter data in CSV format (label,value,color)</label>
              <textarea id="bulk-data" rows="5" placeholder="Red,25,#FF6384&#10;Blue,40,#36A2EB&#10;Yellow,35,#FFCE56"></textarea>
              <button id="apply-bulk" style="margin-top: 10px;">Apply Bulk Data</button>
            </div>
          </div>
          
          <div class="tab-content" data-tab="appearance">
            <div class="form-group">
              <label for="chart-type">Chart Type</label>
              <select id="chart-type">
                <option value="pie">Pie Chart</option>
                <option value="doughnut">Doughnut Chart</option>
              </select>
            </div>
            
            <div class="form-group">
              <label for="legend-position">Legend Position</label>
              <select id="legend-position">
                <option value="top">Top</option>
                <option value="right" selected>Right</option>
                <option value="bottom">Bottom</option>
                <option value="left">Left</option>
              </select>
            </div>
            
            <div class="form-group">
              <label for="font-size">Font Size</label>
              <input type="range" id="font-size" min="8" max="24" value="12">
              <span id="font-size-value">12px</span>
            </div>
            
            <div class="form-group">
              <label for="border-width">Border Width</label>
              <input type="range" id="border-width" min="0" max="10" value="1">
              <span id="border-width-value">1px</span>
            </div>
            
            <div class="form-group">
              <label>Animation</label>
              <div>
                <input type="checkbox" id="animate-rotation" checked>
                <label for="animate-rotation">Animate Rotation</label>
              </div>
              <div>
                <input type="checkbox" id="animate-scale" checked>
                <label for="animate-scale">Animate Scale</label>
              </div>
            </div>
          </div>
          
          <div class="actions">
            <button id="apply-changes" class="btn-success">Apply Changes</button>
            <button id="reset" class="btn-secondary">Reset</button>
          </div>
        </div>
        
        <div class="chart-container">
          <canvas id="pie-chart"></canvas>
        </div>
      </div>
    `;

    // Add initial data rows
    this.addDataRow('Red', 25, '#FF6384');
    this.addDataRow('Blue', 40, '#36A2EB');
    this.addDataRow('Yellow', 35, '#FFCE56');

    // Initialize the chart
    this.initChart();
  }

  addDataRow(label = '', value = '', color = '') {
    const dataEntriesContainer = this.shadowRoot.querySelector('#data-entries');
    const rowIndex = dataEntriesContainer.children.length;
    
    if (!color) {
      color = this.defaultColors[rowIndex % this.defaultColors.length];
    }
    
    const rowElement = document.createElement('div');
    rowElement.className = 'data-row';
    rowElement.innerHTML = `
      <input type="text" class="label-input" placeholder="Label" value="${label}">
      <input type="number" class="value-input" placeholder="Value" value="${value}">
      <input type="color" class="color-picker" value="${color}">
      <div class="delete-btn">×</div>
    `;
    
    const deleteBtn = rowElement.querySelector('.delete-btn');
    deleteBtn.addEventListener('click', () => {
      dataEntriesContainer.removeChild(rowElement);
    });
    
    dataEntriesContainer.appendChild(rowElement);
  }

  setupEventListeners() {
    // Tab switching
    const tabs = this.shadowRoot.querySelectorAll('.tab');
    tabs.forEach(tab => {
      tab.addEventListener('click', () => {
        this.shadowRoot.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
        this.shadowRoot.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
        
        tab.classList.add('active');
        const tabName = tab.getAttribute('data-tab');
        this.shadowRoot.querySelector(`.tab-content[data-tab="${tabName}"]`).classList.add('active');
      });
    });

    // Add data row
    const addDataRowBtn = this.shadowRoot.querySelector('#add-data-row');
    addDataRowBtn.addEventListener('click', () => {
      this.addDataRow();
    });

    // Bulk add toggle
    const bulkAddBtn = this.shadowRoot.querySelector('#bulk-add');
    const bulkAddContainer = this.shadowRoot.querySelector('#bulk-add-container');
    bulkAddBtn.addEventListener('click', () => {
      bulkAddContainer.style.display = bulkAddContainer.style.display === 'none' ? 'block' : 'none';
    });

    // Apply bulk data
    const applyBulkBtn = this.shadowRoot.querySelector('#apply-bulk');
    applyBulkBtn.addEventListener('click', () => {
      const bulkData = this.shadowRoot.querySelector('#bulk-data').value;
      const rows = bulkData.split('\n');
      
      // Clear existing rows
      const dataEntriesContainer = this.shadowRoot.querySelector('#data-entries');
      dataEntriesContainer.innerHTML = '';
      
      // Add new rows
      rows.forEach(row => {
        if (row.trim()) {
          const [label, value, color] = row.split(',').map(item => item.trim());
          this.addDataRow(label, value, color);
        }
      });
      
      bulkAddContainer.style.display = 'none';
    });

    // Import data
    const importDataBtn = this.shadowRoot.querySelector('#import-data');
    importDataBtn.addEventListener('click', () => {
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = '.json,.csv';
      
      input.addEventListener('change', (event) => {
        const file = event.target.files[0];
        if (!file) return;
        
        const reader = new FileReader();
        reader.onload = (e) => {
          try {
            if (file.name.endsWith('.json')) {
              const importedData = JSON.parse(e.target.result);
              this.importJsonData(importedData);
            } else if (file.name.endsWith('.csv')) {
              const csvData = e.target.result;
              this.importCsvData(csvData);
            }
          } catch (error) {
            console.error('Error importing data:', error);
            alert('Error importing data. Please check the file format.');
          }
        };
        
        reader.readAsText(file);
      });
      
      input.click();
    });

    // Export data
    const exportDataBtn = this.shadowRoot.querySelector('#export-data');
    exportDataBtn.addEventListener('click', () => {
      const data = this.collectChartData();
      const dataStr = JSON.stringify(data, null, 2);
      const dataUri = 'data:application/json;charset=utf-8,'+ encodeURIComponent(dataStr);
      
      const exportLink = document.createElement('a');
      exportLink.setAttribute('href', dataUri);
      exportLink.setAttribute('download', 'chart-data.json');
      exportLink.click();
    });

    // Range input updates
    const fontSizeInput = this.shadowRoot.querySelector('#font-size');
    const fontSizeValue = this.shadowRoot.querySelector('#font-size-value');
    fontSizeInput.addEventListener('input', () => {
      fontSizeValue.textContent = `${fontSizeInput.value}px`;
    });

    const borderWidthInput = this.shadowRoot.querySelector('#border-width');
    const borderWidthValue = this.shadowRoot.querySelector('#border-width-value');
    borderWidthInput.addEventListener('input', () => {
      borderWidthValue.textContent = `${borderWidthInput.value}px`;
    });

    // Apply changes
    const applyChangesBtn = this.shadowRoot.querySelector('#apply-changes');
    applyChangesBtn.addEventListener('click', () => {
      this.updateChartFromInputs();
      this.isEditorVisible = false;
      this.shadowRoot.querySelector('.editor').style.display = 'none';
      this.shadowRoot.querySelector('.edit-button').style.display = 'block';
    });

    // Reset button
    const resetBtn = this.shadowRoot.querySelector('#reset');
    resetBtn.addEventListener('click', () => {
      if (confirm('Are you sure you want to reset the chart to default values?')) {
        this.resetToDefaults();
      }
    });

    // Edit button
    const editButton = this.shadowRoot.querySelector('.edit-button');
    editButton.addEventListener('click', () => {
      this.isEditorVisible = true;
      this.shadowRoot.querySelector('.editor').style.display = 'block';
      editButton.style.display = 'none';
    });
  }

  importJsonData(data) {
    try {
      // Update chart title
      if (data.title) {
        this.shadowRoot.querySelector('#chart-title').value = data.title;
      }
      
      // Clear existing rows
      const dataEntriesContainer = this.shadowRoot.querySelector('#data-entries');
      dataEntriesContainer.innerHTML = '';
      
      // Add data rows
      if (data.labels && data.datasets && data.datasets.length > 0) {
        const dataset = data.datasets[0];
        for (let i = 0; i < data.labels.length; i++) {
          const label = data.labels[i];
          const value = dataset.data[i];
          const color = dataset.backgroundColor[i];
          this.addDataRow(label, value, color);
        }
      }
      
      // Update appearance settings if available
      if (data.options) {
        if (data.options.legend && data.options.legend.position) {
          this.shadowRoot.querySelector('#legend-position').value = data.options.legend.position;
        }
        
        if (data.options.legend && data.options.legend.labels && data.options.legend.labels.fontSize) {
          const fontSize = data.options.legend.labels.fontSize;
          this.shadowRoot.querySelector('#font-size').value = fontSize;
          this.shadowRoot.querySelector('#font-size-value').textContent = `${fontSize}px`;
        }
        
        if (data.datasets && data.datasets.length > 0 && data.datasets[0].borderWidth !== undefined) {
          const borderWidth = data.datasets[0].borderWidth;
          this.shadowRoot.querySelector('#border-width').value = borderWidth;
          this.shadowRoot.querySelector('#border-width-value').textContent = `${borderWidth}px`;
        }
        
        if (data.type) {
          this.shadowRoot.querySelector('#chart-type').value = data.type === 'doughnut' ? 'doughnut' : 'pie';
        }
        
        if (data.options.animation) {
          this.shadowRoot.querySelector('#animate-rotation').checked = 
            data.options.animation.animateRotate !== undefined ? data.options.animation.animateRotate : true;
          this.shadowRoot.querySelector('#animate-scale').checked = 
            data.options.animation.animateScale !== undefined ? data.options.animation.animateScale : true;
        }
      }
    } catch (error) {
      console.error('Error processing imported JSON:', error);
      alert('Error processing imported data. Please check the file format.');
    }
  }

  importCsvData(csvData) {
    const rows = csvData.split('\n');
    
    // Clear existing rows
    const dataEntriesContainer = this.shadowRoot.querySelector('#data-entries');
    dataEntriesContainer.innerHTML = '';
    
    // Add new rows from CSV
    rows.forEach(row => {
      if (row.trim()) {
        const [label, value, color] = row.split(',').map(item => item.trim());
        this.addDataRow(label, value, color || '');
      }
    });
  }

  collectChartData() {
    const dataRows = this.shadowRoot.querySelectorAll('.data-row');
    const labels = [];
    const data = [];
    const backgroundColor = [];
    const hoverBackgroundColor = [];
    
    dataRows.forEach(row => {
      const label = row.querySelector('.label-input').value;
      const value = parseFloat(row.querySelector('.value-input').value);
      const color = row.querySelector('.color-picker').value;
      
      if (label && !isNaN(value)) {
        labels.push(label);
        data.push(value);
        backgroundColor.push(color);
        hoverBackgroundColor.push(color);
      }
    });
    
    const chartTitle = this.shadowRoot.querySelector('#chart-title').value;
    const chartType = this.shadowRoot.querySelector('#chart-type').value;
    const legendPosition = this.shadowRoot.querySelector('#legend-position').value;
    const fontSize = parseInt(this.shadowRoot.querySelector('#font-size').value);
    const borderWidth = parseInt(this.shadowRoot.querySelector('#border-width').value);
    const animateRotation = this.shadowRoot.querySelector('#animate-rotation').checked;
    const animateScale = this.shadowRoot.querySelector('#animate-scale').checked;
    
    return {
      type: chartType,
      title: chartTitle,
      labels: labels,
      datasets: [{
        data: data,
        backgroundColor: backgroundColor,
        hoverBackgroundColor: hoverBackgroundColor,
        borderWidth: borderWidth
      }],
      options: {
        legend: {
          position: legendPosition,
          labels: {
            fontSize: fontSize,
            fontFamily: 'Arial'
          }
        },
        title: {
          display: true,
          text: chartTitle,
          fontSize: fontSize + 6,
          fontFamily: 'Arial'
        },
        animation: {
          animateRotate: animateRotation,
          animateScale: animateScale
        }
      }
    };
  }

  updateChartFromInputs() {
    const chartData = this.collectChartData();
    
    // Update chart configuration
    this.chartData.labels = chartData.labels;
    this.chartData.datasets[0].data = chartData.datasets[0].data;
    this.chartData.datasets[0].backgroundColor = chartData.datasets[0].backgroundColor;
    this.chartData.datasets[0].hoverBackgroundColor = chartData.datasets[0].hoverBackgroundColor;
    this.chartData.datasets[0].borderWidth = chartData.datasets[0].borderWidth;
    
    this.chartOptions.legend.position = chartData.options.legend.position;
    this.chartOptions.legend.labels.fontSize = chartData.options.legend.labels.fontSize;
    this.chartOptions.title.text = chartData.options.title.text;
    this.chartOptions.title.fontSize = chartData.options.title.fontSize;
    this.chartOptions.animation.animateRotate = chartData.options.animation.animateRotate;
    this.chartOptions.animation.animateScale = chartData.options.animation.animateScale;
    
    // Update chart type
    if (this.chartInstance) {
      this.chartInstance.destroy();
    }
    
    this.initChart(chartData.type);
  }

  resetToDefaults() {
    // Reset chart title
    this.shadowRoot.querySelector('#chart-title').value = 'My Pie Chart';
    
    // Reset data rows
    const dataEntriesContainer = this.shadowRoot.querySelector('#data-entries');
    dataEntriesContainer.innerHTML = '';
    this.addDataRow('Red', 25, '#FF6384');
    this.addDataRow('Blue', 40, '#36A2EB');
    this.addDataRow('Yellow', 35, '#FFCE56');
    
    // Reset appearance settings
    this.shadowRoot.querySelector('#chart-type').value = 'pie';
    this.shadowRoot.querySelector('#legend-position').value = 'right';
    
    const fontSizeInput = this.shadowRoot.querySelector('#font-size');
    fontSizeInput.value = 12;
    this.shadowRoot.querySelector('#font-size-value').textContent = '12px';
    
    const borderWidthInput = this.shadowRoot.querySelector('#border-width');
    borderWidthInput.value = 1;
    this.shadowRoot.querySelector('#border-width-value').textContent = '1px';
    
    this.shadowRoot.querySelector('#animate-rotation').checked = true;
    this.shadowRoot.querySelector('#animate-scale').checked = true;
    
    // Update chart
    this.updateChartFromInputs();
  }

  initChart(type = 'pie') {
    if (!window.Chart) {
      console.error('Chart.js is not loaded. Please include Chart.js in your project.');
      return;
    }
    
    const canvas = this.shadowRoot.querySelector('#pie-chart');
    const ctx = canvas.getContext('2d');
    
    if (this.chartInstance) {
      this.chartInstance.destroy();
    }
    
    this.chartInstance = new Chart(ctx, {
      type: type,
      data: this.chartData,
      options: this.chartOptions
    });
  }

  // Public methods for external access
  setData(data) {
    try {
      this.importJsonData(data);
      this.updateChartFromInputs();
      return true;
    } catch (error) {
      console.error('Error setting data:', error);
      return false;
    }
  }
  
  getData() {
    return this.collectChartData();
  }
  
  showEditor() {
    this.isEditorVisible = true;
    this.shadowRoot.querySelector('.editor').style.display = 'block';
    this.shadowRoot.querySelector('.edit-button').style.display = 'none';
  }
  
  hideEditor() {
    this.isEditorVisible = false;
    this.shadowRoot.querySelector('.editor').style.display = 'none';
    this.shadowRoot.querySelector('.edit-button').style.display = 'block';
  }
}

// Define the custom element
customElements.define('wix-pie-chart-builder', AdvancedPieChartBuilder);
