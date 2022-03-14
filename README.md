# Remix

I am trying out Remix. This readme contains my account of the experience. The
rest of the readme below a horizontal line is the original generated readme.

The first thing to note is that Remix uses a source generator a scaffold a new
project. This is similar to CRA. I don't like this way of starting off projects,
but it seems to be the new thing now. At least as far as source generators go,
the Remix one is decent; it allows generating the project in the directory it
was run from (by specifying `.`), which some others do not.

It seemed as though Remix was going to host the application for me, I chose the
Remix App Server option in the generator. I tried to figure out how that works,
but the generated readme does not cover this and the `package.json` scripts do
not have any deployment script either.

It seems that running `npm run build` gives you a production-ready static file
server friendly output, which is expected, but the generator really made it seem
like there was going to be another script for deploying to a Remix hosted site.

I went to the [Deployment guide] and it said to read the readme… Not helpful as
the readme doesn't cover this in an understandable way. I guess the output of
`npm run build` drops two directories, `build` and `public/build`, but it is not
immediately clear how to run these on my own server. Do I use a Remix command in
the same directory where I put them? Or do I just run a static file server? I'll
try to find out later.

[Deployment guide]: https://remix.run/docs/en/v1/guides/deployment

But still, I did not choose to host on a static file server, I chose Remix App
Server in the prompt. What _is_ that? Deployment guide did not answer this, it
pointed me to the readme which did not answer it and it all starts to feel like
the "mom told me to ask dad who told me to ask mom" meme.

Onwards past this deployment hurdle. Let's run locally first and see what's up.
`npm start` seems to be a command to run the production build, if I'm guessing
correctly. This is an unfortunate choice in my opinion, but I'll be able to live
with `npm run dev`.

This command runs the development build and it binds to all interfaces. This is
cool, because it in theory means I can test the app on my phone on my home wi-fi
network right away. In practice, my phone couldn't reach the page, but this is
most certainly down to my shitty router, so I can't blame Remix for that.

That scaffolded basic app is very spartan, but I like that. No frivolous styles
to remove right out of the gate.

I am working on adding a `/posts` route to my app now. The tutorial suggests
using a `Link` component which is at this point IMO not needed. It renders out
the same as using simple `a[href]`. I wish people would default to this and only
use `Link` when it brings a benefit. Remix seems to be all about using HTML
first and augmenting it, so why not do that here?

The suggested implementation of the `/posts` handler, the `posts/index.tsx`
component, uses `export default function`. This is my preferred way and it is
nice to see in the sea of no-default-exports cargo culting.

The data loading thing up next is interesting. I am not a fan of the named
export of the `loader`. I guess this is a hardcoded name? Renaming it breaks the
data loading, so I suppose that answers that.

It's cool how a form of server-side rendering is built into Remix. Refreshing
the page with the data loader wired up prints the console log statement on both
the server and the client. We can see why that is when we check the source code
of the page as returned from the server (Firefox right click > View Page Source)
and in it, the `routeData` field on the global `window.__remixContext` object.
Serializing this data into the HTML is the server hit, hydrating the React app
is the client hit. Both call the `render` method of the component.

I say this is a form of SSR, not SSR, because I have not yet verified actually
rendering the data into the component will render HTML on the server, not just
serialize a bunch of JS useless for SEO. Let's see about that next.

I followed the next part of the tutorial and it showed how to render the data
and add TypeScript types nicely. Indeed the SSR works as expected and the result
would be very SEO-friendly, that's nice to see.

The `app/post.ts` thing is not my style, I will reserve judgement for now to see
how it feels to me after having used it for a bit. So far so good, the only nit
is that at this point in the tutorial, the `loader` has been too `async`-happy
and now with the call to `getPosts` also `await`-happy. Similarly to the
premature use of `Link`, let's hold off on these until they are actually needed.

Oh and what is up with the `~` imports? Is this a Webpack thing being adopted by
Remix? Or is that a part of ESM I didn't know about? It really doesn't look like
the latter and I think we can all agree we don't need more of the former. Bummer
this is. I am not going to like this I don't think. :(

Onto [Pulling from a data source]. I am pleased to learn that VS Code will do
syntax highlighting for MarkDown frontmatter. I don't use it a whole lot, so I
had no idea, but I might start.

[Pulling from a data source]: https://remix.run/docs/en/v1/tutorials/blog#pulling-from-a-data-source

`tiny-invariant` is also interesting, although in general I don't like the idea
of using packages for runtime checks. TypeScript should have a mode to emit
runtime checks with some supporting syntax if necessary, but ideally just based
on the explicit or inferred types. Oh well, here we are.

Ah, finally a reason to make the `loader` async/await! I'm not psyched about the
tutorial introducing TypeScript errors and then going on to fix them. I guess it
is good for learning, but to me it just gives me a pause thinking there is an
error in the tutorial only to learn I could have saved my typing and copy-pasted
the next step right away.

I like the speed of the Remix local server. The application is proper snappy.

The dynamic route arguments seem okay. The separation of the logic dealing with
the FS into `post.ts` makes more sense now, but I still don't think I will like
or indeed use this pattern myself. I will stick to my function per file habit.
Looking at the import list of `post.ts` with the addition of `marked` hints at
why I prefer this. A single list of a bunch of dependencies, many of which only
a sole function will use.

Alright, overall so far so good, Remix seems nice and simple. Let's advance.
[Creating Blog Posts]. As a side-note, who decided it was a good idea to make
the headings in the tutorial not selectable? It's a joy a go into the Dev Tools
just to copy-paste the heading text for the purpose of the MarkDown link here.

[Creating Blog Posts]: https://remix.run/docs/en/v1/tutorials/blog#creating-blog-posts

On no, styles. I was praising Remix for not including too much right off the bat
in the source code generator. I would have added that I don't think separating
the styles sheet files that are 1:1 with the component files in a different
directory than the components themselves doesn't seem like a great idea. I know
they don't technically need to be 1:1 and this pattern is more general, so I see
the appeal, but really, for components, it's in my opinion best to keep the code
and style files side-by-side. Also, importing the style only to re-export it in
the `links` special-case export? I already don't like `loader`, `links` is not
going to fare better. That being said though, this is not worse than Next's
`Head` element and stuff like that. Of course nothing beats just adding a
`style` element to your component, but I do understand that the idea here is to
place all of the stylesheets into the `head` element and do not have duplication
in the `body` in case the component appears multiple times. Anyway, styles are
meh right now.

This idea with nested routes is pretty cool. This is what makes Remix stand out,
finally. I am not sure yet if I'll like it, but I can already respect it, even
if I have to supress some ASP .NET master page memories. Of course, the nested
routes use client-side navigation (now there's a reason to use `Link` over `a`)
as confirmed by watching the network log with persistence enabled to make sure
the Remix dev server wasn't just super duper fast.

This next whole section about accepting the form submission and binding the
action data to the component's backend was impossible for me to read due to the
TypeScript errors I mentioned before. I _am_ going to go fixing them before I
continue reading, or at least skip ahead and copy-paste in fixes for them
without having read the stuff in-between. Now all that was achieved is that I
have skipped over a part of the tutorial. The TypeScript errors are not the
golden kernel at the core of the tutorial, there's no need to withold the fixes
for them for the big reveal. They are just types. Just provide the working
snippet and then explain stuff.

Anyway, I think I get it just based on the copy-paste, we have another hardcoded
named export that gets called when we submit a form, we add another method to
`post.ts` and we add more runtime checks that make the code ugly.

It almost worked on the first try, I just forgot to add an import of `json` from
Remix to `post.ts` for `createPost` which beautifully shows how this class is no
service or anything like that if it works with concrete types for HTML responses
like that. There's no benefit to be had from centralizing these CRUD functions
in a single file like this.

Oh and the post creation logic doesn't add main headers to the post files so now
we have the previous posts with explicit header and new posts with no header
which we could pull from the metadata and display in an `h1` but then the old
posts would have a double header. Another nit, but just why?


---

# Welcome to Remix!

- [Remix Docs](https://remix.run/docs)

## Development

From your terminal:

```sh
npm run dev
```

This starts your app in development mode, rebuilding assets on file changes.

## Deployment

First, build your app for production:

```sh
npm run build
```

Then run the app in production mode:

```sh
npm start
```

Now you'll need to pick a host to deploy it to.

### DIY

If you're familiar with deploying node applications, the built-in Remix app server is production-ready.

Make sure to deploy the output of `remix build`

- `build/`
- `public/build/`

### Using a Template

When you ran `npx create-remix@latest` there were a few choices for hosting. You can run that again to create a new project, then copy over your `app/` folder to the new project that's pre-configured for your target server.

```sh
cd ..
# create a new project, and pick a pre-configured host
npx create-remix@latest
cd my-new-remix-app
# remove the new project's app (not the old one!)
rm -rf app
# copy your app over
cp -R ../my-old-remix-app/app app
```
