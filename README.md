
What This App Shows

This app helps you learn how Shiny updates plots. It has three tabs to show different ways.
How Shiny Works

Shiny apps normally update the plot every time you change an input—like the slider or title. This can be slow (see Tab 1). With eventReactive() , you can control when the plot updates—like waiting for a button click (Tabs 2 and 3). A setting called ignoreNULL decides if the plot shows when the app starts or waits for a click. The title starts empty, but that doesn’t change things here—it’s about the button.
The Tabs

    No EventReactive : The plot updates every time you move the slider, pick a color, change the number, or type a title. It’s slow and not smooth.
    ignoreNULL = TRUE : The plot only updates when you click 'Render Plot'. It waits for you to click. This is the usual way.
    ignoreNULL = FALSE : The plot shows when the app starts and updates every time you click 'Render Plot'. It doesn’t wait.

What ignoreNULL Means

    ignoreNULL = TRUE : The plot only updates when you click the 'Render Plot' button. It waits for you to click. This is the default.
    ignoreNULL = FALSE : The plot shows when the app starts and updates every time you click the button. It doesn’t wait.

Try It

Use the tabs. Change the inputs, leave the title blank or type something, and click the button in Tabs 2 and 3. See when the plot updates. Look at the code on the left to see how eventReactive() and ignoreNULL work.
