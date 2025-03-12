library(shiny)
library(ggplot2)

# Define UI
ui <- fluidPage(
  titlePanel("Understanding eventReactive and ignoreNULL"),
  tabsetPanel(
    # Tab 0: Readme
    tabPanel(
      "Readme",
      fluidRow(
        column(12,
               htmlOutput("readme")
        )
      )
    ),
    # Tab 1: No EventReactive
    tabPanel(
      "No EventReactive",
      fluidRow(
        column(4,
               sliderInput("size1", "Sample Size:", 10, 1000, 100),
               selectInput("color1", "Bar Color:", c("Red" = "red", "Blue" = "blue", "Green" = "green")),
               numericInput("mult1", "Multiplier:", 1, min = 1, max = 10),
               textInput("title1", "Plot Title:", value = "")  # NULL by default
        ),
        column(8,
               plotOutput("plot1")
        )
      )
    ),
    # Tab 2: EventReactive with ignoreNULL = TRUE
    tabPanel(
      "ignoreNULL = TRUE",
      fluidRow(
        column(4,
               sliderInput("size2", "Sample Size:", 10, 1000, 100),
               selectInput("color2", "Bar Color:", c("Red" = "red", "Blue" = "blue", "Green" = "green")),
               numericInput("mult2", "Multiplier:", 1, min = 1, max = 10),
               textInput("title2", "Plot Title:", value = ""),
               actionButton("render2", "Render Plot")
        ),
        column(8,
               plotOutput("plot2")
        )
      )
    ),
    # Tab 3: EventReactive with ignoreNULL = FALSE
    tabPanel(
      "ignoreNULL = FALSE",
      fluidRow(
        column(4,
               sliderInput("size3", "Sample Size:", 10, 1000, 100),
               selectInput("color3", "Bar Color:", c("Red" = "red", "Blue" = "blue", "Green" = "green")),
               numericInput("mult3", "Multiplier:", 1, min = 1, max = 10),
               textInput("title3", "Plot Title:", value = ""),
               actionButton("render3", "Render Plot")
        ),
        column(8,
               plotOutput("plot3")
        )
      )
    )
  )
)

# Define Server Logic
server <- function(input, output) {
  # Readme Tab Content with Simple English
  output$readme <- renderUI({
    tagList(
      h3("What This App Shows"),
      p("This app helps you learn how Shiny updates plots. It has three tabs to show different ways."),
      h4("How Shiny Works"),
      p("Shiny apps normally update the plot every time you change an input—like the slider or title. This can be slow (see Tab 1). With ", tags$code("eventReactive()"), ", you can control when the plot updates—like waiting for a button click (Tabs 2 and 3). A setting called ", tags$code("ignoreNULL"), " decides if the plot shows when the app starts or waits for a click. The title starts empty, but that doesn’t change things here—it’s about the button."),
      h4("The Tabs"),
      tags$ul(
        tags$li(
          strong("No EventReactive"), ": The plot updates every time you move the slider, pick a color, change the number, or type a title. It’s slow and not smooth."
        ),
        tags$li(
          strong("ignoreNULL = TRUE"), ": The plot only updates when you click 'Render Plot'. It waits for you to click. This is the usual way."
        ),
        tags$li(
          strong("ignoreNULL = FALSE"), ": The plot shows when the app starts and updates every time you click 'Render Plot'. It doesn’t wait."
        )
      ),
      h4("What ignoreNULL Means"),
      tags$ul(
        tags$li(
          strong("ignoreNULL = TRUE"), ": The plot only updates when you click the 'Render Plot' button. It waits for you to click. This is the default."
        ),
        tags$li(
          strong("ignoreNULL = FALSE"), ": The plot shows when the app starts and updates every time you click the button. It doesn’t wait."
        )
      ),
      h4("Try It"),
      p("Use the tabs. Change the inputs, leave the title blank or type something, and click the button in Tabs 2 and 3. See when the plot updates. Look at the code on the left to see how ", tags$code("eventReactive()"), " and ", tags$code("ignoreNULL"), " work.")
    )
  })
  
  # Tab 1: No EventReactive
  output$plot1 <- renderPlot({
    Sys.sleep(0.5)
    data <- rnorm(input$size1) * input$mult1
    ggplot(data.frame(x = 1:input$size1, y = data), aes(x, y)) +
      geom_bar(stat = "identity", fill = input$color1) +
      labs(title = if (input$title1 == "") "No Title" else input$title1)
  })
  
  # Tab 2: EventReactive with ignoreNULL = TRUE
  plot_data2 <- eventReactive(input$render2, ignoreNULL = TRUE, {
    Sys.sleep(0.5)
    data <- rnorm(input$size2) * input$mult2
    list(data = data, color = input$color2, size = input$size2, title = input$title2)
  })
  output$plot2 <- renderPlot({
    ggplot(data.frame(x = 1:plot_data2()$size, y = plot_data2()$data), aes(x, y)) +
      geom_bar(stat = "identity", fill = plot_data2()$color) +
      labs(title = if (plot_data2()$title == "") "No Title" else plot_data2()$title)
  })
  
  # Tab 3: EventReactive with ignoreNULL = FALSE
  plot_data3 <- eventReactive(input$render3, ignoreNULL = FALSE, {
    Sys.sleep(0.5)
    data <- rnorm(input$size3) * input$mult3
    list(data = data, color = input$color3, size = input$size3, title = input$title3)
  })
  output$plot3 <- renderPlot({
    ggplot(data.frame(x = 1:plot_data3()$size, y = plot_data3()$data), aes(x, y)) +
      geom_bar(stat = "identity", fill = plot_data3()$color) +
      labs(title = if (plot_data3()$title == "") "No Title" else plot_data3()$title)
  })
}

# Run the App in Showcase Mode
shinyApp(ui, server, options = list(display.mode = "showcase"))
