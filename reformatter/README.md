I never thought I'd see the day when the #1 pain point of working with PowerPoint in a group of people, let alone a large organization like Microsoft, would be solvable. On Saturday, I helped a customer work through the problem, applying Copilot in PowerPoint with the latest models. Let me share how we approached the solution, and ask you to crowdsource improvements by contributing to this repo!
 
# The problem: Template and formatting hell
I know a lot of you already know exactly what I'm about to say in this section. If by reading the headline alone, you already fully understand and relate to the problem space, skip to the next section. 
  
PowerPoint's most powerful features--masters, layouts, themes, and color palettes--are also the leading cause of frustration for anyone who works in PowerPoint, particularly people who assemble presentations from slides that have been created using other decks (which have their own collections of masters, layouts, themes...).
 
As you assemble a deck from multiple sources, you have a Sophie's Choice to make: "Paste and use destination theme" which blows up the formatting of the slides you're bringing in to your deck; or "Paste and keep source formatting", which ends up making your deck look exactly what it is: a Frankensteined patchwork of disconnected slides. No matter which choice you make, if you want to deliver a coherent and consistently designed presentation, you're likely going to be spending a lot of time doing manual formatting to reconcile differences.

# My customer's particularly hellish hell
The impetus for this solution was a customer who creates online learning for medical professionals using presentations from professional conferences. So these are decks created by doctors. Let's just say...

> I'm a doctor, Jim! Not a PowerPoint expert!

Yeah: the source decks were beyond tragic. To date, it has been a mammoth, lengthy process to manually reformat each deck into something that looks as professional as the content; and that aligns with the high design standards for all courses the online learning portal.

# How we approached building the solution
We spent approximately two hours building the first version of the solution using Copilot in PowerPoint. At the Microsoft 365 Community Conference, I was lucky enough to be recruited to demo Copilot in PowerPoint for Jeff Teper's keynote and, from doing so, had a sense that the technology might finally be up to the task.
 
First step was manual: We opened one of the ultra-messy "doctor decks" and added a **slide master** that reflected the desired look-and-feel with **layouts** that reflected common layouts used in doctor decks. This represented our **target formatting**.
 
The end-goal here was to **convert a sloppy slide into a sizzing slide**, with as much automation as possible. But, particularly during iterative development of our automation, we wanted to preserve the original for reference, so we could compare the original to the target slide.
 
So the first prompt we tried asked copilot to look at a single slide, duplicate it (to preserve the original) and, on the copy, to **visually examine the source slide and identify the target layout that best reflected the intent of the author**. This is important because, often, people will create slides in wonky workflows, for example starting with a blank slide and dropping text boxes for the slide title and bullet points, instead of using a layout with title and content placeholders. By considering the intent of the slide, rather than the specific way it was created, we can remove that variance.
 
The prompt also instruced to **apply that layout, and reset the slide**. PowerPoint's "reset" action removes directly applied formatting including positioning, sizing, and font formats. It effectively snaps the slide to the formatting of the layout.
 
We executed the prompt on several slides and WOW! we were very impressed by the accuracy of Copilot's choices.
 
The next step was to try to reflect the author's **intent regarding text placement**. Far too many PowerPoint creators will create text with 'extra lines' between paragraphs ("Hit <return> twice") to create whitespace between points. For our doctors' slides, they might put a citation at the bottom of the slide by pressing enter 10+ times until the text was at the bottom (ugh!).
 
Our slide master had proper line spacing (using the Line Spacing command; adding space before and/or after). So we prompted to **examine text spacing and the intent of the author**. If the whitespace was "one line", remove any extra line breaks. If the intent seemed to be to **position the text somewhere specific**, like the bottom of the slide, then position it correctly, accordingly.
 
Next was the **text formatting itself: font size, typeface, color, emphasis (bold, italic, underline, highlight)**. And OMG these doctors' slides used every combination under the sun, sometimes on a single slide. The "reset formatting" already removed all formatting, resetting it to the fonts in the layout and theme. Now we had to reapply formatting thoughtfully, again based on the intent of the author. 
 
We landed on this as our first approach:
- For font size, if the source slide had text in a significantly different size from other text at the same outline level, reflect that in the target under the assumption it is intentional. 
- If an entire paragraph is formatted uniquely, do not reapply the formatting. Keep the paragraph aligned to the layout and theme. This resolved variances where, for example, a slide might have a patient story in itialics that wasn't the standard for the online course.
- But if text within a paragraph was bold, italic, or underline, we reapplied the formatting to preserve it.
- If text was colorized, either a font color or a hilight color, we standardized it to a single font color.
 
Next was images. The target design had a slide background that was not pure white. That means that any image of a screenshot or a journal article (anything with a white background) will create a hard visual edge against the background. So we **added drop shadows to all images**.
 
Finally, citations, which there are a lot of in this customer's source decks. The customer does not want full citations visible on the slides during the course, but rather references on slides with citations centralized to an appendix slide. Problem: every author uses a different format, placement, etc. Individual authors even applied different formats for citations on different slides. So we visually **look for anything that is a citation**, and move it off the screen. The customer has since updated the skill to create an appendix with each citation and references. 
 
With these instructions, so far, we were already well on our way to saving our customer hours of work per deck.

# ProTip: Agent logging and learning
As we tested each improved prompt, we could see in Copilot's response how it thought about the prompt, how it applied the instructions, and how it evaluated the results. We fed the learning into each subsequent revision to the prompt. But as we readied ourselves for Copilot to iterate over multiple slides--even an entire deck--we would lose that learning.
 
So we added to our prompt an instruction that Copilot **add a text box, off to the side of the slide (outside the visible area), and log what it did and why**: which layout it picked, which formatting it reset, and which formatting it reappplied.  That way, when the agent completed its work, and we were comparing the original slides to the target slides, **if something wasn't quite right we could learn why, improve the prompt accordingly, and retry** the prompt on the same source slide.
 
So the system became, in a way, **self-aware and self-improving** with a little help from Copilot's human companions.

# What's next
This will be an **iterative process of learning from the agent's output, finding the next set of gaps and variances, improving the prompt, and trying again**. With this particular scenario, it's hard to imagine an agent making 100% perfect choices every time because far too many people (doctors, not PowerPoint experts) are involved. But we can prioritize where to invest in improvements and get closer each run.
 
Right now we have a massive prompt that works quite well--nay, surprisingly well. There is not yet a good way to **turn this into a replicable automation** (e.g. a "skill" for Copilot in PowerPoint). Any ideas??
 
One of the areas for rich improvement will be **color choice for text and graphics**. PowerPoint non-experts don't realize that accent colors are dynamic, based on the theme. Accent Color 3 might be red in your current master, but blue in the new master. All text you formatted in red using Accent Color 3 will now be blue after applying the new master. That's probably the right choice for most body text. But if you've created a stop sign shape using an octagon shape with red fill (Accent color 3) and text "Stop" that is white (Text 1), you could end up with a blue stop sign with black text in your new formatting. So we will build logic that evaluates whether the intent of the author was a specific color, in which case the correct action would be to apply red as a proper color, not a theme color.
 
And we will tackle **modern formatting, including rounded corners**. PowerPoint's UX doesn't expose rounded corner radius and, generally, designers won't want the same radius applied to a box the size of the slide itself and for a box that's a fraction of the slide's area. But agents can access the setting!
 
Automating **intelligent visual element usage**. Think of how many marketing slides have a date statement like "Coming <date>". Now think about how many different ways that information is visually coveyed in a single deck. Copilot can recognize date statements and treat them all the same way within a deck.
 
What ideas do you have? Share them in the replies!

# Copy the solution for your work
I'm sharing the prompt, in its entirety, below. This was the result of only two hours of learning; and there is much more room for improvement. 

**The "deal" is if you copy it, you contribute to it.**

# Prerequisite
You must have a **slide master with layouts** that approximate the types of layouts source slides might have. The more aligned your layouts are to the source material, the more accurate that early step of identifying the best layout will be, and the less "tweaking" you'll have to do later.
 
The prompt below assumes you have already **added the target master to the source deck**. There are several ways to do this. One is to copy any slide from the target deck, paste into the source deck and choose "Keep Source Formatting". **Rename the master "TARGET"** (or change the reference in the prompt). You could also work on the prompt to load from the target master template directly, for example by providing a link to the .potx or .pptx.
 
The most important thing for this process are layouts with placeholders and relative font sizes. Everything else can be easily modified later. I will loop back here and share my favorite masters and layouts with you.