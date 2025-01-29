# What's a first class citizen?

After having heard the Lex Fridman podcast episode with the [cursor.ai](https://lexfridman.com/cursor-team) I was excitedly talking about this with a good friend. In reponse to my attempt to boil down what I think cursor is trying to do, I said something like 'they're putting AI at the core of the editor, as a first-class citizen - not an extension'. To which he replied 'so that is that *really* - a 'first-class citizen?'. Good question.

[Wikipedia](https://en.wikipedia.org/wiki/First-class_citizen#:~:text=In%20a%20given%20programming%20language,and%20assigned%20to%20a%20variable.) says:

> In a given programming language design, a first-class citizen is an entity which supports all the operations generally available to other entities. These operations typically > include being passed as an argument, returned from a function, and assigned to a variable.

Probably oversimplified, I think this roughly translates to 'the stuff that's IN the language out-of-the-box, native to the language'. Whether that is fully accurate or not, I think the concepts and implications are the most interesting of this. What does it really mean when somehting is first-class? 

Some recent developments that remind me of first-class vs second-class effects:


- GitHub co-pilot is an [extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat) for Visual Studio Code (VSCode). The team at Cursor forked the repository because they couldn't integrate genAI deep enough into the product, into the user experience for their liking. Which is why they made it central to the product as opposed to an extension. They're building the editor around the AI capabilities as opposed to providing it as an extension. 

- Microsoft launched [CoreAI](https://blogs.microsoft.com/blog/2025/01/13/introducing-core-ai-platform-and-tools/) as an organization next to the Azure organization. That reminds of elevating the AI capabilities from the Azure services product groups, to a dedicated team and organization. Thusfar, genAI capabilities were incorporated in all the products and services within Azure, now it's been pulled out into it's separate organisation. I'm very curious what see what the second order consequences are of this.

- Whether or not [sustainability](https://learn.microsoft.com/en-us/azure/well-architected/pillars) is a separate pillar of the Well Architected Framework. Both [Microsoft](https://learn.microsoft.com/en-us/azure/well-architected/) and [Google Cloud](https://cloud.google.com/architecture/framework) do not consider sustainability as a separate pillar in the well architected framework. [AWS](https://aws.amazon.com/architecture/well-architected/?wa-lens-whitepapers.sort-by=item.additionalFields.sortDate&wa-lens-whitepapers.sort-order=desc&wa-guidance-whitepapers.sort-by=item.additionalFields.sortDate&wa-guidance-whitepapers.sort-order=desc) does. 
That is not to say Azure and GCP don't have extensive documentation about sutainability, one just does it a bit more. It elevates sustainability to a peer of security as opposed to one of the side-effects of operational efficiency.

- Lastly, and one actually related to programming languages, the introduction of `async` keywords in a language. I learnt about async in C# and it was very cool to see this paradigm coming to JavaScript and Python. Yes, it was probably possible to do async calls in these languages as well, but introducing async as keyword made it first-class.

It's not always easy to detect this. Especially 'in the beginning', when something is elevated to first-class, the differences seem small. One could think 'cursor is not that different from GitHub co-pilot', or 'MSFT is just moving teams around' and 'whether sustainability is a separate pillar or not is a semantic discussion'. And those arguments are hard to counter (in the early stages especially). But this these are boring challenges in my opinion and do not honor the profound change and the interesting new direction that was chosen.

I think the second-order consequences of first-class are the very interesting. I'm not entirely sure this is an exhaustive list, but these are the things I think of:

- It expresses intent. It shows a mind-shift and change of thinking. 'This thing is here to stay and it's important'
- Gives it a sense of urgency. It shines a bigger light on something, promotes adoption. It's less easy to ignore.
- 

So my antenna is up for things that 


