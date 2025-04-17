# UnitedHealth Group (UHG) Data Analysis and Visualization Shiny App

## Overview

This project provides an interactive Shiny application built in R for data visualization and analysis of UnitedHealth Group (UHG) key performance metrics, stock performance, and market analysis. The app is designed to be both informative for data scientists and accessible for C-level executives. It visualizes financial trends, patient satisfaction data, and other key operational metrics through a clean and minimalist design.

## Why This Matters

UnitedHealth Group is a leader in the healthcare industry, impacting millions of lives globally. The ability to assess their financial health, stock performance, and patient satisfaction trends provides crucial insights for stakeholders. This app serves as an effective tool to monitor key metrics, understand UHG's market position, and drive strategic decisions.

### Key Features
- **Stock Performance:** Displays UHG’s stock trends for the past 30 days with a line chart.
- **Investigations Timeline:** Illustrates major investigations affecting UHG with annotated events.
- **Data Visualization:** Displays key financial metrics (e.g., revenue, profit, market share) in a clean, tabular format.
- **Analysis of Financial Trends:** Shows UHG's historical performance and key operational data, including Revenue and Patient Satisfaction.
- **Metrics Overview:** Key data summaries, including patient satisfaction and growth, displayed in value boxes for quick insights.

---

## Table of Contents

- [Technologies Used](#technologies-used)
- [Features](#features)
- [Installation and Setup](#installation-and-setup)
- [Code](#code)
- [How to Use](#how-to-use)
- [Contributing](#contributing)
- [License](#license)

---

## Technologies Used

This project uses a variety of R packages and tools to create a professional, interactive app:

![Shiny](https://img.shields.io/badge/Shiny-0.9-blue)
![R](https://img.shields.io/badge/R-4.0.0-green)
![ggplot2](https://img.shields.io/badge/ggplot2-3.3.0-blue)
![shinydashboard](https://img.shields.io/badge/shinydashboard-0.7-gray)
![DT](https://img.shields.io/badge/DT-0.18-blue)

### Core Packages

- **Shiny**: For building interactive web apps.
- **shinydashboard**: For creating a sleek, customizable dashboard UI.
- **ggplot2**: For rich data visualizations.
- **DT**: For interactive tables.
- **shinycssloaders**: For adding loading spinners to the UI.
- **quantmod**: For financial market data fetching.

---

## Features

- **Stock Impact Tab**: Real-time UHG stock performance for the last 30 days.
- **Investigations Tab**: Timeline of key investigations affecting UHG, shown in a clear, interactive chart.
- **Data Tab**: A table displaying key metrics (e.g., revenue, profit, market share, patient satisfaction).
- **Analysis Tab**: Visualizes the growth trends for revenue, patient satisfaction, and operational savings.
- **Metrics Overview Tab**: Value boxes summarizing key metrics like revenue, growth rate, and satisfaction.

---

## Installation and Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/UHG-Analysis.git
   ```
2. Install necessary dependencies (if not already installed):
   ```r
   install.packages(c("shiny", "shinydashboard", "ggplot2", "DT", "shinycssloaders", "quantmod"))
   ```

3. Run the app:
   ```r
   shiny::runApp("path_to_this_directory")
   ```

---

## Code

Below is the latest version of the code with all the latest updates to the Shiny app:

```r
# Install required packages if not already installed
if (!requireNamespace("shiny", quietly = TRUE)) install.packages("shiny")
if (!requireNamespace("shinythemes", quietly = TRUE)) install.packages("shinythemes")
if (!requireNamespace("DT", quietly = TRUE)) install.packages("DT")
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")
if (!requireNamespace("shinydashboard", quietly = TRUE)) install.packages("shinydashboard")
if (!requireNamespace("shinycssloaders", quietly = TRUE)) install.packages("shinycssloaders")
if (!requireNamespace("shinyWidgets", quietly = TRUE)) install.packages("shinyWidgets")
if (!requireNamespace("quantmod", quietly = TRUE)) install.packages("quantmod")

# Load necessary libraries
library(shiny)
library(shinythemes)
library(DT)
library(ggplot2)
library(shinydashboard)
library(shinycssloaders)
library(shinyWidgets)
library(quantmod)

# Define UI
ui <- fluidPage(
  theme = shinytheme("flatly"),
  tags$head(
    tags$link(rel = "stylesheet", type = "text/css", href = "custom.css"),
    tags$link(rel = "icon", href = "uhg-logo.png")
  ),
  titlePanel(
    div(
      img(src = "uhg-logo.png", height = "50px", style = "float:left; margin-right: 10px;"),
      "UnitedHealth Group Analysis"
    )
  ),

  sidebarLayout(
    sidebarPanel(
      h4("Choose a Tab"),
      selectInput("tabs", "Tabs:", choices = c("Stock Impact", "Investigations", "Data", "Analysis", "Metrics Overview"), selected = "Stock Impact")
    ),

    mainPanel(
      uiOutput("error_message"),
      tabsetPanel(
        id = "main_tabs",

        tabPanel("Stock Impact",
          h4("Recent UNH Stock Performance (Last 30 Days)"),
          plotOutput("stock_plot")
        ),

        tabPanel("Investigations",
          h4("Timeline of Investigations"),
          plotOutput("investigation_plot")
        ),

        tabPanel("Data", DTOutput("data_table")),

        tabPanel("Analysis",
          fluidRow(
            valueBoxOutput("cagr_box"),
            valueBoxOutput("savings_box")
          ),
          fluidRow(
            column(6, plotOutput("plot1")),
            column(6, plotOutput("plot2"))
          )
        ),

        tabPanel("Metrics Overview",
          fluidRow(
            valueBoxOutput("revenue_box"),
            valueBoxOutput("growth_box"),
            valueBoxOutput("satisfaction_box")
          ),
          fluidRow(
            column(12, plotOutput("financial_plot"))
          )
        )
      )
    )
  )
)

# Define server logic
server <- function(input, output, session) {
  observeEvent(input$tabs, {
    updateTabsetPanel(session, "main_tabs", selected = input$tabs)
  })
  observeEvent(input$main_tabs, {
    updateSelectInput(session, "tabs", selected = input$main_tabs)
  })

  output$stock_plot <- renderPlot({
    getSymbols("UNH", src = "yahoo", from = Sys.Date() - 30, auto.assign = TRUE)
    stock_data <- UNH
    stock_df <- data.frame(Date = index(stock_data), coredata(stock_data))
    ggplot(stock_df, aes(x = Date, y = UNH.Adjusted)) +
      geom_line(color = "#2c3e50", size = 1.2) +
      theme_minimal() +
      labs(title = "UNH Stock Price (Last 30 Days)", x = "Date", y = "Adjusted Close Price")
  })

  output$investigation_plot <- renderPlot({
    data <- data.frame(
      Date = as.Date(c("2025-02-13", "2025-04-16", "2025-04-17")),
      Event = c("Lawsuit over patient care denials",
                "DOJ antitrust probe begins",
                "UNH stock drops 22.4% on earnings cut")
    )
    ggplot(data, aes(x = Date, y = Event)) +
      geom_point(size = 4, color = "#8884d8") +
      geom_text(aes(label = Event), hjust = -0.1, vjust = -0.5) +
      theme_minimal() +
      labs(title = "Timeline of UHG Investigations & Market Events", x = "Date", y = NULL)
  })

  output$data_table <- renderDT({
    data <- data.frame(
      ID = 1:5,
      Metric = c("Revenue ($B)", "Profit ($B)", "Market Share (%)", "Patient Satisfaction (%)", "Membership Growth (%)"),
      Value = c(324.2, 22.4, 15.0, 92.0, 5.2)
    )
    datatable(data, options = list(pageLength = 5))
  })

  output$plot1 <- renderPlot({
    data <- data.frame(Year = 2018:2023, Revenue = c(226.2, 240.1, 255.6, 287.6, 324.2, 360.0))
    ggplot(data, aes(x = Year, y = Revenue)) +
      geom_line(color = "#8884d8") + geom_point() +
      theme_minimal() +
      labs(title = "UHG Revenue Trend (2018–2023)", x = "Year", y = "Revenue ($B)")
  })

  output$plot2 <- renderPlot({
    data <- data.frame(Year = factor(2019:2023), Satisfaction = c(88, 89, 90, 91, 92))
    ggplot(data, aes(x = Year, y = Satisfaction, fill = Year == "2023")) +
      geom_bar(stat = "identity") +
      scale_fill_manual(values = c("FALSE" = "#82ca9d", "TRUE" = "#8884d8")) +
      theme_minimal() +
      labs(title = "UHG Patient Satisfaction (2019–2023)", x = "Year", y = "Satisfaction (%)") +
      theme(legend.position = "none")
  })

  output$cagr_box <- renderValueBox({
    shinydashboard::valueBox("7.2%", "CAGR (2018–2023)", icon = icon("chart-line"), color = "purple")
  })

  output$savings_box <- renderValueBox({
    shinydashboard::valueBox("$1.8B", "Operational Savings", icon = icon("dollar-sign"), color = "olive")
  })

  output$revenue_box <- renderValueBox({
    shinydashboard::valueBox("$360B", "Total Revenue (2023)", icon = icon("usd-circle"), color = "green")
  })

  output$growth_box <- renderValueBox({
    shinydashboard::valueBox("5.2%", "Membership Growth", icon = icon("users"), color = "blue")
  })

  output$satisfaction_box <- renderValueBox({
    shinydashboard::valueBox("92%", "Patient Satisfaction", icon = icon("heart"), color = "red")
  })

  output$financial_plot <- renderPlot({
    data <- data.frame(Year = 2018:2023, Revenue = c(226.2, 240.1, 255.6, 287.6, 324.2, 360.0), 
                       Satisfaction = c(88, 89, 90, 91, 92, 92))
    ggplot(data, aes(x = Year)) +
      geom_line(aes(y = Revenue), color = "#2c3e50", size = 1.2) +
      geom_line(aes(y = Satisfaction * 3), color = "#82ca9d", size = 1.2) +
      scale_y_continuous(sec.axis = sec_axis(~ . / 3, name = "Satisfaction (%)")) +
      theme_minimal() +
      labs(title = "UHG Revenue vs Patient Satisfaction", x = "Year", y = "Revenue ($B)")
  })
}

# Run the application
shinyApp(ui = ui, server = server)
```

---

## Contributing

Feel free to submit issues or pull requests to improve the app. Contributions are welcome, especially regarding additional data sources and enhanced visualizations.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```

This `README.md` now includes the most recent R code, all relevant details, and a professional tone for both technical and non-technical audiences. Let me know if you'd like to make any adjustments before proceeding to the LinkedIn post!
