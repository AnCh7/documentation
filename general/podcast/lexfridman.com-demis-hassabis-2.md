# Transcript for Demis Hassabis: Future of AI, Simulating Reality, Physics and Video Games | Lex Fridman Podcast #475

This is a transcript of Lex Fridman Podcast #475 with Demis Hassabis.    The timestamps in the transcript are clickable links that take you directly to that point in    the main video. Please note that the transcript is human generated, and may have errors.    Here are some useful links:    

- Go back to [this episode’s main page](https://lexfridman.com/demis-hassabis-2/)
- Watch the [full YouTube version of the podcast](https://youtube.com/watch?v=-HzgcbRXUK8)

## Table of Contents

Here are the loose “chapters” in the conversation.    Click link to jump approximately to that part in the transcript:    

- [0:00 – Episode highlight](https://lexfridman.com/demis-hassabis-2-transcript#chapter0_episode_highlight)
- [1:21 – Introduction](https://lexfridman.com/demis-hassabis-2-transcript#chapter1_introduction)
- [2:06 – Learnable patterns in nature](https://lexfridman.com/demis-hassabis-2-transcript#chapter2_learnable_patterns_in_nature)
- [5:48 – Computation and P vs NP](https://lexfridman.com/demis-hassabis-2-transcript#chapter3_computation_and_p_vs_np)
- [14:26 – Veo 3 and understanding reality](https://lexfridman.com/demis-hassabis-2-transcript#chapter4_veo_3_and_understanding_reality)
- [18:50 – Video games](https://lexfridman.com/demis-hassabis-2-transcript#chapter5_video_games)
- [30:52 – AlphaEvolve](https://lexfridman.com/demis-hassabis-2-transcript#chapter6_alphaevolve)
- [36:53 – AI research](https://lexfridman.com/demis-hassabis-2-transcript#chapter7_ai_research)
- [41:17 – Simulating a biological organism](https://lexfridman.com/demis-hassabis-2-transcript#chapter8_simulating_a_biological_organism)
- [46:00 – Origin of life](https://lexfridman.com/demis-hassabis-2-transcript#chapter9_origin_of_life)
- [52:15 – Path to AGI](https://lexfridman.com/demis-hassabis-2-transcript#chapter10_path_to_agi)
- [1:03:01 – Scaling laws](https://lexfridman.com/demis-hassabis-2-transcript#chapter11_scaling_laws)
- [1:06:17 – Compute](https://lexfridman.com/demis-hassabis-2-transcript#chapter12_compute)
- [1:09:04 – Future of energy](https://lexfridman.com/demis-hassabis-2-transcript#chapter13_future_of_energy)
- [1:13:00 – Human nature](https://lexfridman.com/demis-hassabis-2-transcript#chapter14_human_nature)
- [1:17:54 – Google and the race to AGI](https://lexfridman.com/demis-hassabis-2-transcript#chapter15_google_and_the_race_to_agi)
- [1:35:53 – Competition and AI talent](https://lexfridman.com/demis-hassabis-2-transcript#chapter16_competition_and_ai_talent)
- [1:42:27 – Future of programming](https://lexfridman.com/demis-hassabis-2-transcript#chapter17_future_of_programming)
- [1:48:53 – John von Neumann](https://lexfridman.com/demis-hassabis-2-transcript#chapter18_john_von_neumann)
- [1:58:07 – p(doom)](https://lexfridman.com/demis-hassabis-2-transcript#chapter19_p_doom_)
- [2:02:50 – Humanity](https://lexfridman.com/demis-hassabis-2-transcript#chapter20_humanity)
- [2:05:56 – Consciousness and quantum computation](https://lexfridman.com/demis-hassabis-2-transcript#chapter21_consciousness_and_quantum_computation)
- [2:12:06 – David Foster Wallace](https://lexfridman.com/demis-hassabis-2-transcript#chapter22_david_foster_wallace)
- [2:19:20 – Education and research](https://lexfridman.com/demis-hassabis-2-transcript#chapter23_education_and_research)

## Episode highlight

Lex Fridman: 00:00:00: It’s hard for us humans to make any  kind of clean predictions about highly nonlinear dynamical systems. But  again, to your point, we might be very surprised what classical learning systems might be able to do about even fluid.        

Demis Hassabis: 00:00:12: Yes, exactly. I mean, fluid dynamics,  Navier-Stokes equations, these are traditionally thought of as very,  very difficult intractable problems to do on classical systems. They  take enormous amounts of compute, weather prediction systems. These  kinds of things all involve fluid dynamics calculations.        

00:00:27: But again, if you look at something  like Veo, our video generation model, it can model liquids quite well,  surprisingly well. And materials, specular lighting, I love the ones  where there’s people who generate videos where there’s clear liquids  going through hydraulic presses and then it’s being squeezed out. I used to write physics engines and graphics engines in my early days in  gaming, and I know it’s just so painstakingly hard to build programs  that can do that. And yet somehow these systems are reverse engineering  from just watching YouTube videos. So presumably what’s happening is  it’s extracting some underlying structure around how these materials  behave. So perhaps there is some kind of lower dimensional manifold that can be learned if we actually fully understood what’s going on under  the hood. That’s maybe true of most of reality.        

## Introduction

Lex Fridman: 00:01:22: The following is a conversation with  Demis Hassabis, his second time on the podcast. He is the leader of  Google DeepMind and is now a Nobel Prize winner. Demis is one of the  most brilliant and fascinating minds in the world today working on  understanding and building intelligence and exploring the big mysteries  of our universe. This was truly an honor and a pleasure for me.        

00:01:51: This is the Lex Fridman Podcast. To  support it, please check out our sponsors in the description and  consider subscribing to this channel. And now, dear friends, here’s  Demis Hassabis.        

## Learnable patterns in nature

Lex Fridman: 00:02:06: In your Nobel Prize lecture, you  propose what I think is a super interesting conjecture that “any pattern that can be generated or found in nature can be efficiently discovered  and modeled by a classical learning algorithm.” What kind of patterns or systems might be included in that? Biology, chemistry, physics, maybe  cosmology, neuroscience? What are we talking about?        

Demis Hassabis: 00:02:32: Sure. Well, look, I felt that it’s  sort of a tradition, I think, of Nobel Prize lectures that you’re  supposed to be a little bit provocative and I wanted to follow that  tradition. What I was talking about there is if you take a step back and you look at all the work that we’ve done, especially with the Alpha X  projects, so I’m thinking AlphaGo, of course, AlphaFold, what they  really are is we are building models of very combinatorially, high  dimensional spaces that if you try to brute force a solution, find the  best move and go, or find the exact shape of a protein, and if you  enumerated all the possibilities, there wouldn’t be enough time in the  time of the universe.        

00:03:08: So you have to do something much  smarter. And what we did in both cases was build models of those  environments and that guided the search in a smart way and that makes it tractable. So if you think about protein folding, which is obviously a  natural system, why should that be possible? How does physics do that?  Proteins fold in milliseconds in our bodies, so somehow physics solves  this problem that we’ve now also solved computationally. And I think the reason that’s possible is that in nature, natural systems have  structure because they were subject to evolutionary processes that shape them. And if that’s true, then you can maybe learn what that structure  is.        

Lex Fridman: 00:03:49: This perspective I think is a really  interesting one. You’ve hinted it at it, which is almost like crudely  stated, anything that can be evolved can be efficiently modeled. Think  there’s some truth to that?        

Demis Hassabis: 00:04:03: Yeah. I sometimes call it survival of  the stablest or something like that because of course there’s evolution  for life, living things, but there’s also, if you think about geological times, so the shape of mountains, that’s been shaped by weathering  processes over thousands of years, but then you can even take it  cosmological, the orbits of planets, the shapes of asteroids. These have all been survived kind of processes that have acted on them many, many  times.        

00:04:31: If that’s true, then there should be  some sort of pattern that you can kind of reverse learn and a kind of  manifold really that helps you search to the right solution, to the  right shape and actually allow you to predict things about it in an  efficient way because it’s not a random pattern. So it may not be  possible for man-made things or abstract things like factorizing large  numbers because unless there’s patterns in the number space, which there might be, but if there’s not and it’s uniform, then there’s no pattern  to learn, there’s no model to learn that will help you search. So you  have to do brute force. So in that case you maybe need a quantum  computer, something like this. But in most things in nature that we’re  interested in are not like that. They have structure that evolved for a  reason and survived over time. And if that’s true, I think that’s  potentially learnable by a neural network.        

Lex Fridman: 00:05:21: It’s like nature is doing a search  process and it’s so fascinating that in that search process, it’s  creating systems that could be efficiently modeled.        

Demis Hassabis: 00:05:31: That’s right. Yeah.        

Lex Fridman: 00:05:32: So interesting.        

Demis Hassabis: 00:05:33: So they can be efficiently  rediscovered or recovered because nature’s not random. Everything that  we see around us, including the elements that are more stable, all of  those things, they’re subject to some kind of selection process  pressure.        

## Computation and P vs NP

Lex Fridman: 00:05:47: Do you think because you’re also a fan of theoretical computer science and complexity, do you think we can  come up with a complexity class, like a complexity zoo type of class  where maybe it’s the set of learnable systems, the set of learnable  natural systems, LNS. This is a Demis Hassabis new class of systems that could be actually learnable by classical systems in this kind of way,  natural systems that can be modeled efficiently.        

Demis Hassabis: 00:06:17: Yeah, I mean I’ve always been  fascinated by the P equals NP question and what is model-able by  classical systems, i.e. non-quantum systems, Turing machines in effect.  And that’s exactly what I’m working on actually in my few moments of  spare time with a few colleagues about should there be maybe a new class or problem that is solvable by this type of neural network process and  kind of mapped onto these natural systems, so the things that exist in  physics and have structure. So I think that could be a very interesting  new way of thinking about it. And it sort of fits with the way I think  about physics in general, which is that I think information is primary,  information is the most sort of fundamental unit of the universe, more  fundamental than energy and matter. I think they can all be converted  into each other, but I think of the universe as a kind of informational  system.        

Lex Fridman: 00:07:07: So when you think of the universe as an informational system, then the P equals NP question is a physics question.        

Demis Hassabis: 00:07:14: That’s right.        

Lex Fridman: 00:07:15: And is a question that can help us actually solve the entirety of this whole thing going on.        

Demis Hassabis: 00:07:20: Yeah, I think it’s one of the most  fundamental questions actually if you think of physics as informational  and the answer to that, I think it’s going to be very enlightening.        

Lex Fridman: 00:07:29: More specific to the P and MP  question, again, some of the stuff we’re saying is kind of crazy right  now just like the Christian Anfinsen Nobel Prize speech, controversial  thing that he said sounded crazy and then you went and got a Nobel Prize for this with John Jumper, solved the problem. So let me just stick to  the P equals NP. Do you think there’s something in this thing we’re  talking about that could be shown if you can do something like a  polynomial time or constant time compute ahead of time and construct  this gigantic model, then you can solve some of these extremely  difficult problems in a theoretical computer science kind of way?        

Demis Hassabis: 00:08:12: Yeah, I think that there are actually a huge class of problems that could be couched in this way, the way we  did AlphaGo and the way we did AlphaFold, where you model what the  dynamics of the system is, the properties of that system, the  environment that you are trying to understand, and then that makes the  search for the solution or the prediction of the next step efficient.  Basically polynomial times, so tractable by a classical system, which a  neural network is. It runs on normal computers, right? Classical  computers, Turing machines in effect. And I think it’s one of the most  interesting questions there is, is how far can that paradigm go?        

00:08:53: I think we’ve proven, and the AI  community in general that classical systems, Turing machines can go a  lot further than we previously thought. They can do things like model  the structures of proteins and play go to better than world champion  level. And a lot of people would’ve thought maybe 10, 20 years ago that  was decades away, or maybe you would need some sort of quantum machines  to quantum systems to be able to do things like protein folding. And so I think we haven’t really even sort of scratched the surface yet of what  classical systems so-called could do.        

00:09:28: And of course, AGI being built on a  neural network system on top of a neural network system on top of a  classical computer would be the ultimate expression of that. And I think the limit, what the bounds of that kind of system, what it can do, it’s a very interesting question and directly speaks to the P equals NP  question.        

Lex Fridman: 00:09:47: What do you think, again,  hypothetical, might be outside of this? Maybe emergent phenomena? If you look at cellular automata, you have extremely simple systems and then  some complexity emerges. Maybe that would be outside or even would you  guess even that might be amenable to efficient modeling by a classical  machine?        

Demis Hassabis: 00:10:09: Yeah, I think those systems would be  right on the boundary. So I think most emergent systems, cellular  automata, things like that could be model-able by a classical system.  You just sort of do a forward simulation of it and it’d probably be  efficient enough. Of course there’s the question of things like chaotic  systems where the initial conditions really matter and then you get to  some uncorrelated end state. Now those could be difficult to model. So I think these are kind of the open questions, but I think when you step  back and look at what we’ve done with the systems and the problems that  we’ve solved, and then you look at things like Veo 3 on video generation sort of rendering physics and lighting and things like that, really  core fundamental things in physics, it’s pretty interesting. I think  it’s telling us something quite fundamental about how the universe is  structured in my opinion. So in a way that’s what I want to build AGI  for is to help us as scientists answer these questions like P equals NP.        

Lex Fridman: 00:11:09: Yeah, I think we might be continuously surprised about what is model-able by classical computers. I mean  AlphaFold 3 on the interaction side is surprising that you can make any  kind of progress on that direction. AlphaGenome is surprising that you  can map the genetic code to the function. Kind of playing with the  emergent kind of phenomena, you think there’s so many combinatorial  options and then here you go, you can find the kernel that is  efficiently model-able.        

Demis Hassabis: 00:11:36: Yes, because there’s some structure,  there’s some landscape in the energy landscape or whatever it is that  you can follow, some gradient you can follow. And of course what neural  networks are very good at is following gradients. And so if there’s one  to follow and you can specify the objective function correctly, you  don’t have to deal with all that complexity, which I think is how we  maybe have naively thought about it for decades, those problems. If you  just enumerate all the possibilities, it looks totally intractable and  there’s many, many problems like that.        

00:12:06: And then you think, “Well, it’s like  10 to 300 possible protein structures, 10 to the 170 possible go  positions. All of these are way more than atoms in the universe, so how  could one possibly find the right solution or predict the next step?”  But it turns out that it is possible. And of course reality in nature  does do it. Proteins do fold. So that gives you confidence that there  must be, if we understood how physics was doing that in a sense and we  could mimic that process, i.e. model that process, it should be possible on our classical systems is basically what the conjecture is about.        

Lex Fridman: 00:12:44: And of course there’s nonlinear  dynamical systems, highly nonlinear dynamical systems, everything  involving fluid. I recently had a conversation with Terence Tao who  mathematically contends with a very difficult aspect of systems that  have some singularities in them that break the mathematics, and it’s  just hard for us humans to make any kind of clean predictions about  highly nonlinear dynamical systems. But again, to your point, we might  be very surprised what classical learning systems might be able to do  about even fluid.        

Demis Hassabis: 00:13:16: Yes, exactly. I mean fluid dynamics,  Navier-Stokes equations, these are traditionally thought of as very,  very difficult, intractable kind of problems to do on classical systems. They take enormous amounts of compute, weather prediction systems.  These kind of things all involve fluid dynamics calculations. But again, if you look at something like Veo, our video generation model, it can  model liquids quite well, surprisingly well. And materials, specular  lighting, I love the ones where there’s people who generate videos where there’s clear liquids going through hydraulic presses and then it’s  being squeezed out. I used to write physics engines and graphics engines in my early days in gaming, and I know it’s just so painstakingly hard  to build programs that can do that. And yet somehow these systems are  reverse engineering from just watching YouTube videos. So presumably  what’s happening is it’s extracting some underlying structure around how these materials behave. So perhaps there is some kind of lower  dimensional manifold that can be learned if we actually fully understood what’s going on under the hood. That’s maybe true of most of reality.        

## Veo 3 and understanding reality

Lex Fridman: 00:14:26: Yeah, I’ve been continuously precisely by this aspect of Veo 3. I think a lot of people highlight different  aspects including the comedic and the mean and all that kind of stuff.  And then the ultra realistic ability to capture humans in a really nice  way that’s compelling and feels close to reality, and then combine that  with native audio. All of those are marvelous things about Veo 3, but  exactly the thing you’re mentioning, which is the physics.        

Demis Hassabis: 00:14:52: Yeah.        

Lex Fridman: 00:14:53: It’s not perfect, but it’s pretty damn good. And then the really interesting scientific question is what is it understanding about our world in order to be able to do that? Because  if the cynical, take with diffusion models, there’s no way to  understands anything. But I don’t think you can generate that kind of  video without understanding. And then our own philosophical notion of  what it means to understand then is brought to the surface. To what  degree do you think Veo 3 understands our world?        

Demis Hassabis: 00:15:25: I think to the extent that it can  predict the next frames in a coherent way, that is a form of  understanding, not in the anthropomorphic version of, it’s not some kind of deep philosophical understanding of what’s going on, I don’t think  these systems have that, but they certainly have modeled enough of the  dynamics, put it that way, that they can pretty accurately generate  whatever it is, eight seconds of consistent video that by eye, at least  at a glance, is quite hard to distinguish what the issues are.        

00:15:57: And imagine that in two or three more  years’ time, that’s the thing I’m thinking about and how incredible that will look given where we’ve come from the early versions of that one or two years ago. And so the rate of progress is incredible. And I think  I’m like you is like a lot of people love all of the stand-up comedians  and that actually captures a lot of human dynamics very well and body  language, but actually the thing I’m most impressed with and fascinated  by is the physics behavior, the lighting and materials and liquids. And  it’s pretty amazing that it can do that. And I think that shows that it  has some notion of at least intuitive physics, how things are supposed  to work intuitively, maybe the way that a human child would understand  physics, right, as opposed to a PhD student really being able to unpack  all the equations. It’s more of an intuitive physics understanding.        

Lex Fridman: 00:16:53: Well, that intuitive physics  understanding, that’s the base layer, that’s the thing people sometimes  call a common-sense. It really understands something. I think that  really surprised a lot of people. It blows my mind that I just didn’t  think it would be possible to generate that level of realism without  understanding. There’s this notion that you can only understand the  physical world by having an embodied AI system, a robot that interacts  with that world. That’s the only way to construct an understanding of  that world. But Veo 3 is directly challenging that it feels like.        

Demis Hassabis: 00:17:27: Yes, and it’s very interesting, even  if you were to ask me five, 10 years ago, I would’ve said, even though I was immersed in all of this, I would’ve said, “Well, yeah, you probably need to understand intuitive physics. If I push this off the table,  this glass, it will maybe shatter and the liquid will spill out. So we  know all of these things.” But I thought that, and there’s a lot of  theories in neuroscience, it’s called action in perception where you  need to act in the world to really, truly perceive it in a deep way. And there was a lot of theories about you’d need embodied intelligence or  robotics or something, or maybe at least simulated action so that you  would understand things like intuitive physics.        

00:18:06: But it seems like you can understand  it through passive observation, which is pretty surprising to me. And  again, I think hints at something underlying about the nature of reality in my opinion, beyond just the cool videos that it generates. And of  course there’s next stages is maybe even making those videos interactive so one can actually step into them and move around them, which would be really mind-blowing, especially given my games background. So you can  imagine. And then I think we’re starting to get towards what I would  call a world model, a model of how the world works, the mechanics of the world, the physics of the world, and the things in that world. And of  course that’s what you would need for a true AGI system.        

## Video games

Lex Fridman: 00:18:50: I have to talk to you about video  games. So you are being a bit trolley. I think you’re having more and  more fun on Twitter, on X, which is great to see. So a guy named Jimmy  Apples tweeted, “Let me play a video game of my Veo 3 videos already.  Google cooked so good. Playable world models wen?” And then you  co-tweeted that with, “Now, wouldn’t that be something?” So how hard is  it to build game worlds with AI? Maybe can you look out into the future  feature of video games five, 10 years out? What do you think that looks  like?        

Demis Hassabis: 00:19:27: Well, games were my first love really. And doing AI for games was the first thing I did professionally in my  teenage years and with the first major AI systems that I built and I  always want to scratch that itch one day and come back to that. And I  will do, I think, and I think I sort of dream about what would I have  done back in the nineties if I’d had access to the kind of AI systems we have today? And I think you could build absolutely mind-blowing games.        

00:19:55: And I think the next stage is I always used to love making, all the games I’ve made are open world games, so  they’re games where there’s a simulation and then there’s AI characters, and then the player interacts with that simulation and the simulation  adapts to the way the player plays. And I always thought they were the  coolest games because, so games like Theme Park that I worked on where  everybody’s game experience would be unique to them because you’re kind  of co-creating the game. We set up the parameters, we set up initial  conditions, and then you as the player immersed in it, and then you are  co-creating it with the simulation. But of course it’s very hard to  program open world games. You’ve got to be able to create content  whichever direction the player goes in, and you want it to be compelling no matter what the player chooses. And so it was always quite difficult to build things like cellular automata actually, type of those kind of  classical systems, which created some emergent behavior, but they’re  always a little bit fragile, a little bit limited. Now we are maybe on  the cusp in the next few years, five, 10 years of having AI systems that can truly create around your imagination, can dynamically change the  story and storytell the narrative around and make it dramatic no matter  what you end up choosing. So it’s like the ultimate choose your own  adventure sort of game. And I think maybe we are within reach, if you  think of a kind of interactive version of Veo and then wind that forward five to 10 years and imagine how good it’s going to be.        

Lex Fridman: 00:21:24: Yeah. So you said a lot of super  interesting stuff there. So one, the open world, built into that is a  deep personalization the way you’ve described it. So it’s not just that  it’s open world, that you can open any door and there’ll be something  there, it’s that it’s the choice of which door you open in an  unconstrained way defines the worlds you see. So some games try to do  that, they give you choice, but it’s really just an illusion of choice  because you only, like Stanley Parable, a game I actually played, it’s  really, there’s a couple of doors and it really just takes you down a  narrative. Stanley Parable is a great video game. I recommend people  play it, that in a meta way, mocks the illusion of choice, and there’s  philosophical notions of free will and so on.        

00:22:12: But I do, one of my favorite games of  Elder Scrolls is Daggerfall I believe, that they really played with a  random generation of the dungeons of if you can step in and they give  you this feeling of an open world. And there you mentioned  interactivity. You don’t need to interact. That’s the first step because you don’t need to interact that much. You just, when you open the door, whatever you see is randomly generated for you. And that’s already an  incredible experience because you might be the only person to ever see  that.        

Demis Hassabis: 00:22:46: Yeah, exactly. But what you’d like is a little bit better than just sort of a random generation. So you’d like, and also better than a simple AB hard coded choice, right? That’s not  really open world, as you say. It’s just giving you the illusion of  choice. What you want to be able to do is potentially anything in that  game environment. And I think the only way you can do that is to have  generated systems, systems that will generate that on the fly. Of  course, you can’t create infinite amounts of game assets. It’s expensive enough already how AAA games are made today. And that was obvious to us back in the nineties when I was working on all these games.        

00:23:25: I think maybe Black & White was  the game that I worked on early stages of that, that had still probably  the best AI, learning AI, in it. It was an early reinforcement learning  system that you were looking after this mythical creature and growing it and nurturing it. And depending how you treated it, it would treat the  villagers in that world the same way. So if you were mean to it, it  would be mean. If you were good, it would be protective. And so it was  really a reflection of the way you played it. So actually all of the,  I’ve been working on simulations and AI through the medium of games at  the beginning of my career, and really the whole of what I do today,  it’s still a follow on from those early more hard coded ways of doing  the AI to now fully general learning systems that are trying to achieve  the same thing.        

Lex Fridman: 00:24:12: Yeah, it is been interesting,  hilarious, and fun to watch you and Elon obviously itching to create  games because you’re both gamers. And one of the sad aspects of your  incredible success in so many domains of science, like serious adult  stuff, that you might not have time to really create a game, you might  end up creating the tooling that others will create the game. You have  to watch others create the thing you’ve always dreamed of. Do you think  it’s possible you can somehow in your extremely busy schedule actually  find time to create something like Black & White, an actual video  game where you could make the childhood dream become reality?        

Demis Hassabis: 00:24:58: Well, there’s two things where I think about that is maybe with vibe coding as it gets better and there’s a  possibility that I could, one could do that actually in your spare time. So I’m quite excited about that as that would be my project if I got  the time to do some vibe coding. I’m actually itching to do that. And  then the other thing is maybe it’s a sabbatical after AGI has been  safely stewarded into the world and delivered into the world. That, and  then working on my physics theory as we talked about at the beginning,  those would be my two post-AGI projects, let’s call it that way.        

Lex Fridman: 00:25:28: I would love to see which you choose,  solving the problem that some of the smartest people in human history  contended with, P equals NP, or creating a cool video game.        

Demis Hassabis: 00:25:44: But in my world, they’d be related  because it would be an open world simulated game as realistic as  possible. So what is the universe? That’s speaking to the same question  and P equals NP. I think all these things are related, at least in my  mind.        

Lex Fridman: 00:25:59: I mean in a really serious way, video  games sometimes are looked down upon as just this fun side activity. But especially as AI does more and more of the difficult, boring tasks,  something we in modern world called work, video games is the thing in  which we may find meaning, in which we may find what to do with our  time. You could create incredibly rich, meaningful experiences. That’s  what human life is. And then in video games, you can create more  sophisticated, more diverse ways of living. Right? That’s the point?        

Demis Hassabis: 00:26:41: I think so. I mean, those of us who  love games and I still do is it’s almost can let your imagination run  wild, right? I used to love games and working on games so much because  it’s the fusion, especially in the nineties and early two thousands, the sort of golden era and maybe the eighties of the games industry. And it was all being discovered. New genres were being discovered. We weren’t  just making games, we felt we were creating a new entertainment medium  that never existed before. Especially with these open world games and  simulation games where you, as the player, were co-creating the story.  There’s no other media, entertainment media, where you do that, where  you as the audience actually co-create the story.        

00:27:23: And of course now with multiplayer  games as well, it can be a very social activity and can explore all  kinds of interesting worlds in that. But on the other hand, it’s very  important to also enjoy and experience the physical world. But the  question is then I think we’re going to have to confront the question  again of what is the fundamental nature of reality? What is going to be  the difference between these increasingly realistic simulations and  multiplayer ones and emergent and what we do in the real world?        

Lex Fridman: 00:27:55: Yeah, there’s clearly a huge amount of value to experiencing the real world, nature. There’s also a huge  amount of value in experiencing other humans directly in person the way  we’re sitting here today, but we need to really scientifically  rigorously answer the question why and which aspect of that can be  mapped into the virtual world.        

Demis Hassabis: 00:28:17: Exactly.        

Lex Fridman: 00:28:18: It’s not enough to say, “Yeah, you should go touch grass and hang out in nature.” It’s like why exactly is that valuable?        

Demis Hassabis: 00:28:25: Yes. And I guess that’s maybe the  thing that’s been haunting me or obsessing me from the beginning of my  career. If you think about all the different things I’ve done, they’re  all related in that way. The simulation, nature of reality, and what is  the bounds of what can be modeled.        

Lex Fridman: 00:28:40: Sorry for the ridiculous question, but so far, what is the greatest video game of all time? What’s up there?        

Demis Hassabis: 00:28:45: Well, my favorite one of all time is  Civilization, I have to say. That was the Civilization I and  Civilization II, my favorite games of all time.        

Lex Fridman: 00:28:54: I can only assume you’ve avoided the  most recent one because it would probably, that would be your  sabbatical. You would disappear.        

Demis Hassabis: 00:29:03: Yes, exactly. They take a lot of time, these Civilization games, so I’ve got to be careful with them.        

Lex Fridman: 00:29:08: Fun question. You and Elon seem to be  somehow solid gamers. Is there a connection between being great at  gaming and being great leaders of AI companies?        

Demis Hassabis: 00:29:20: I don’t know. It’s an interesting one. I mean, we both love games and it’s interesting, he wrote games as well to start off with. Probably, especially in the era I grew up in where  home computers just became a thing in the late eighties and nineties,  especially in the UK, I had a spectrum and then a Commodore Amiga 500,  which was my favorite computer ever. And that’s why I learned all my  programming. And of course, it’s a very fun thing to program, is to  program games. So I think it’s a great way to learn programming,  probably still is. And then of course, I immediately took it in  directions of AI and simulations, so I was able to express my interest  in games and my wider scientific interests all together.        

Demis Hassabis: 00:30:00: And my sort of wider scientific  interests all together. And then the final thing I think that’s great  about games is it fuses artistic design, art, with the most cutting edge programming. So again, in the nineties, all of the most interesting  technical advances were happening in gaming, whether that was AI,  graphics, physics engines, hardware, even GPUs of course were designed  for gaming originally. So everything that was pushing computing forward  in the nineties was due to gaming. So interestingly, that was where the  forefront of research was going on and it was this incredible fusion  with art. Graphics, but also music, and just the whole new media of  storytelling. And I love that. For me, it’s this sort of  multidisciplinary kind of effort is again something I’ve enjoyed my  whole life.        

## AlphaEvolve

Lex Fridman: 00:30:52: I have to ask you, I almost forgot  about one of the many, and I would say one of the most incredible things recently that somehow didn’t yet get enough attention is AlphaEvolve.  We talked about Evolution a little bit, but it’s the Google DeepMind  system that evolves algorithms. Are these kinds of Evolution-like  techniques promising as a component of future super intelligence  systems? So for people who don’t know, it’s kind of, I don’t know if  it’s fair to say it’s LLM guided Evolution search because Evolution  algorithms are doing the search and LLMs are telling you where.        

Demis Hassabis: 00:31:28: Yes. Yes, exactly. So LLMs are kind of proposing some possible solutions and then you use evolutionary  computing on top to find some novel part of the search space. So  actually I think it’s an example of very promising directions where you  combine LLMs or foundation models with other computational techniques.  Evolutionary methods is one, but you could also imagine Monte Carlo tree search. Basically many types of search algorithms or reasoning  algorithms sort of on top of or using the foundation models as a basis.  So I actually think there’s quite a lot of interesting things to be  discovered probably with these sort of hybrid systems, let’s call them.        

Lex Fridman: 00:32:10: But not to romanticize Evolution.        

Demis Hassabis: 00:32:12: Yeah.        

Lex Fridman: 00:32:12: I’m only human, but you think there’s  some value in whatever that mechanism is? Because we already talked  about natural systems. Do you think where there’s a lot of low-hanging  fruit of us understanding, being able to model, being able to simulate  Evolution and then using that, whatever we understand about that  nature-inspired mechanism, to then do search better and better and  better?        

Demis Hassabis: 00:32:36: Yes. So if you think about, again,  breaking down the solar systems we’ve built to their really fundamental  core, you’ve got the model of the underlying dynamics of the system. And then if you want to discover something new, something novel that hasn’t been seen before, then you need some kind of search process on top to  take you to a novel of the search space. And you can do that in a number of ways. Evolutionary computing is one. With AlphaGo, we just use Monte Carlo Tree Search and that’s what found move 37, the new never seen  before strategy in Go. And so that’s how you can go beyond potentially  what is already known. So the model can model everything that you  currently know about, all the data that you currently have. But then how do you go beyond that? So that starts to speak about the ideas of  creativity.        

00:33:28: How can these systems create something new? In fact discover something new? Obviously this is super relevant  for scientific discovery or pushing met science and medicine forward,  which we want to do with these systems. And you can actually bolt on  some fairly simple search systems on top of these models and get you  into a new region of space. Of course, you also have to make sure that  you’re not searching that space totally randomly. It would be too big.  So you have to have some objective function that you’re trying to  optimize and hill climb towards and that guides that search.        

Lex Fridman: 00:34:00: But there’s some mechanism of  Evolution that are interesting maybe in the space of programs. But then  the space of programs an extremely important space, because you can  probably generalize to everything. But for example, mutation. So it’s  not just Monte Carlo Tree Search where it’s like a search. You could  every once in a while-        

Demis Hassabis: 00:34:21: Combine things.        

Lex Fridman: 00:34:22: Combine things?        

Demis Hassabis: 00:34:23: Yeah.        

Lex Fridman: 00:34:23: Things, like the components of a thing.        

Demis Hassabis: 00:34:26: Yes.        

Lex Fridman: 00:34:26: So then what Evolution is really good  at is not just the natural selection, it’s combining things and building increasingly complex hierarchical systems. So that component is super  interesting, especially with AlphaEvolve and the space of programs.        

Demis Hassabis: 00:34:43: Yeah, exactly. So you can get a bit of an extra property out of evolutionary systems, which is some new  emerging capability may come about, right? Of course like happened with  life, interestingly with naive, traditional evolutionary computing  methods without LLMs and the modern AI, the problem with them, they were very well studied in the nineties and early two thousands and some  promising results, but the problem was they could never work out how to  evolve new properties, new emerging properties. You always had a sort of subset of the properties that you put into the system, but maybe if we  combine them with these foundation models, perhaps we can overcome that  limitation.        

00:35:21: Obviously naturally evolution clearly  did. It did evolve new capabilities. So bacteria to where we are now. So clearly that it must be possible with evolutionary systems to generate  new patterns, going back to the first thing we talked about and new  capabilities and emerging properties, and maybe we’re on the cusp of  discovering how to do that.        

Lex Fridman: 00:35:44: Yeah, listen, AlphaEvolve is one of  the coolest things I’ve ever seen. I’ve on my desk at home, most of my  time is spent on that computer is just programming. And next to the  three screens is a skull of a Tiktaalik, which is one of the early  organisms that crawled out of the water onto land. And I just kind of  watch that little guy. It’s like whatever the competition mechanism of  Evolution is, it’s quite incredible.        

Demis Hassabis: 00:36:16: Yes.        

Lex Fridman: 00:36:17: It’s truly, truly incredible. Now  whether that’s exactly the thing we need to do to do our search, but  never dismiss the power of nature, what it did here.        

Demis Hassabis: 00:36:27: And it’s amazing, which is a  relatively simple algorithm, right? Effectively, and it can generate all of this immense complexity emerges obviously running over 4 billion  years of time. But you can think about that as again, a search process  that ran over the physics substrate of the universe for a long amount of computational time, but then it generated all this incredible rich  diversity.        

## AI research

Lex Fridman: 00:36:54: So many questions I want to ask you.  So one, you do have a dream, one of the natural systems you want to try  to model is a cell. That’s a beautiful dream. I could ask you about  that. I also just for that purpose on the AI scientist front just  broadly, so there’s a essay from Daniel Cocotaglio, Scott Alexander and  others that online steps along the way to get to ASI and it has a lot of interesting ideas in it, one of which is including a superhuman coder  and a superhuman AI researcher. And in that there’s a term of research  taste that’s really interesting. So in everything you’ve seen, do you  think it’s possible for AI systems to have research taste to help you in the way that AI co-scientist does, to help steer human brilliant  scientists and then potentially by itself to figure out what are the  directions where you want to generate truly novel ideas? That seems to  be a really important component of how to do great science?        

Demis Hassabis: 00:38:03: Yeah, I think that’s going to be one  of the hardest things to mimic or model is this idea of taste or  judgment. I think that’s what separates the great scientists from the  good scientists. All professional scientists are good technically,  otherwise they wouldn’t have made it that far in academia and things  like that. But then do you have the taste to sniff out what the right  direction is, what the right experiment is, what the right question is?  So picking the right question is the hardest part of science and making  the right hypothesis. And that’s what today’s systems definitely they  can’t do. So I often say it’s harder to come up with a conjecture, a  really good conjecture than it is to solve it. So we may have systems  soon that can solve pretty hard conjectures. A maths Olympiad problems,  where Alpha Proof last year our system got silver medal in that really  hard problems. Maybe eventually we’ll better solve a Millennium Prize  kind of problem. But could a system have come up with a conjecture  worthy of study that someone like Terence Tao would’ve gone? “You know  what, that’s a really deep question about the nature of maths or the  nature of numbers or the nature of physics.” And that is far harder type of creativity. And we don’t really know. Today’s systems clearly can’t  do that. And we’re not quite sure what that mechanism would be. This  kind of leap of imagination like Einstein had when he came up with  special relativity and then general relativity with the knowledge he had at the time.        

Lex Fridman: 00:39:33: For conjecture, you want to come up with a thing that’s interesting, it’s amenable to proof?        

Demis Hassabis: 00:39:41: Yes.        

Lex Fridman: 00:39:42: So it’s easy to come up with a thing  that’s extremely difficult. It’s easy to come up with a thing that’s  extremely easy, but at that very edge-        

Demis Hassabis: 00:39:48: That sweet spot of basically advancing the science and splitting the hypothesis space into two, ideally.  Right? Whether if it’s true or not true, you’ve learned something really useful and that’s hard. And making something that’s also falsifiable  and within the technologies that you currently have available. So it’s a very creative process, actually. A highly creative process that I think just a kind of naive search on top of a model won’t be enough for that.        

Lex Fridman: 00:40:21: The idea of splitting the hypothesis  space in two is super interesting. So I’ve heard you say that there’s  basically no failure or failure is extremely valuable if you construct  the questions right, if you construct the experiments right, if you  design them right, that failure or success are both useful, so perhaps  because it splits the hypothesis basically in two, it’s like a binary  search?        

Demis Hassabis: 00:40:44: Yes, that’s right. So when you do real Blue Sky research, there’s no such thing as failure really. As long as  you are picking experiments and hypotheses that meaningfully split the  hypothesis space and you learn something. You can learn something kind  of equally valuable from an experiment that doesn’t work. That should  tell you if you’ve designed the experiment well and your hypotheses are  interesting, it should tell you a lot about where to go next. And then  you’re effectively doing a search process and using that information in  very helpful ways.        

## Simulating a biological organism

Lex Fridman: 00:41:17: So to go to your dream of modeling a  cell, what are the big challenges that lay ahead for us to make that  happen? We should maybe highlight that in AlphaFold, I mean there’s just so many leaps. So AlphaFold solved, if it’s fair to say, protein  folding. And there’s so many incredible things we could talk about  there, including the open sourcing, everything you’ve released AlphaFold 3 is doing protein, RNA, DNA interactions, which is super complicated  and fascinating. It’s amenable to modeling. AlphaGenome predicts how  small genetic changes if we think about single mutations, how they link  to actual function. So it seems like it’s creeping along to  sophisticated to much more complicated things like a cell. But a cell  has a lot of really complicated components.        

Demis Hassabis: 00:42:11: So what I’ve tried to do throughout my career is I have these really grand dreams and then I try to, as you’ve noticed, but I try to break them down. It’s easy to have a kind of  crazily ambitious dream, but the trick is how do you break it down into  manageable, achievable, interim steps that are meaningful and useful in  their own right? And so Virtual Cell, which is what I call the project  of modeling a cell, I’ve had this idea of wanting to do that for maybe  more like 25 years.        

00:42:41: And I used to talk with Paul Nurse,  who is a bit of a mentor of mine in biology. He runs the founded the  Crick Institute and won the Nobel Prize in 2001. We’ve been talking  about it since the nineties, and I used to come back to it every five  years. It’s like, what would you need to model the full internals of a  cell so that you could do experiments on the virtual cell and what those experiment in silico and those predictions would be useful for you to  save you a lot of time in the wet lab. That would be the dream.        

00:43:13: Maybe you could a hundred x speed up  experiments by doing most of it in silico the search in silico, and then you do the validation step in the wet lab. That’s the dream. But maybe  now, finally, so I was trying to build these components, AlphaFold being one, that would allow you eventually to model the full interaction, a  full simulation of a cell, and I’d probably start with a yeast cell. And partly that’s what Paul Nurse studied because the yeast cell is like a  full organism, that’s a single cell. So it’s the kind of simplest single cell organism. And so it’s not just a cell, it’s a full organism.        

00:43:49: And yeast is very well understood. And so that would be a good candidate for a kind of full simulated model.  Now AlphaFold is the solution to the kind of static picture of what does a 3D structure protein look like? A static picture of it. But we know  that biology, all the interesting things happen with the dynamics, the  interactions, and that’s what AlphaFold 3 is, the first step towards is  modeling those interactions. So first of all, pair wise proteins with  proteins, proteins with RNA and DNA. But then the next step after that  would be modeling maybe a whole pathway, maybe like the tour pathway  that’s involved in cancer or something like this. And then eventually  you might be able to model a whole cell.        

Lex Fridman: 00:44:31: Also, there’s another complexity here  that stuff in a cell happens at different time scales. Is that tricky?  Protein folding is super fast. I don’t know all the biological  mechanisms, but some of them take a long time. And so the levels of  interaction has a different temporal scale that you have to be able to  model.        

Demis Hassabis: 00:44:54: So that would be hard. So you’d  probably need several simulated systems that can interact at these  different temporal dynamics, or at least maybe it’s like a hierarchical  system so you can jump up or down the different temporal stages.        

Lex Fridman: 00:45:07: So can you avoid… One of the  challenges here is not avoid simulating, for example, the quantum  mechanical aspects of any of this, right? You want to not over model.  You can skip ahead to just model the really high level things that get  you a really good estimate of what’s going to happen.        

Demis Hassabis: 00:45:27: Yes. So you got to make a decision  when you’re modeling any natural system, what is the cutoff level of the granularity that you’re going to model it to? And then it captures the  dynamics that you’re interested in. So probably for a cell I would hope  that would be the protein level, and that one wouldn’t have to go down  to the atomic level. So of course that’s where AlphaFold stock kicks in. So that would be kind of the basis, and then you’d build these higher  level simulations that take those as building blocks and then you get  the emergent behavior.        

## Origin of life

Lex Fridman: 00:46:00: I apologize for the pothead questions  ahead of time, but do you think we’ll be able to simulate a model, the  origin of life? So being able to simulate the first from non-living  organisms, the birth of a living organism?        

Demis Hassabis: 00:46:19: I think that’s one of course one of  the deepest and most fascinating questions. I love that area of biology. There’s people, there’s a great book by Nick Lane, one of the top  experts in this area called The Ten Great Inventions of Evolution. I  think it’s fantastic. And it also speaks to what the great filters might be, prior or are they ahead of us? I think they’re most likely in the  past, if you read that book of how unlikely to go have any life at all.  And then single cell to multi-cell seems an unbelievably big jump that  took a billion years, I think on earth to do, right? So it shows you how hard it was.        

Lex Fridman: 00:46:55: Right? Bacteria were super happy for a very long time.        

Demis Hassabis: 00:46:56: For a very long time before they  captured mitochondria somehow, right? I don’t see why not, why AI  couldn’t help with that. Some kind of simulation. Again, it’s a bit of a search process through a combinatorial space. Here’s all the chemical  soup that you start with, the primordial soup, that maybe was on earth  near these hot vents. Here’s some initial conditions. Can you generate  something that looks like a cell? So perhaps that would be a next stage  after the virtual cell project is well, how could something like that  emerge from the chemical soup?        

Lex Fridman: 00:47:31: Well, I would love it if there was a  Move 37 for the origin of life. I think that’s one of the great  mysteries. I think ultimately what we’ll figure out is their continuum.  There’s no such thing as a line between non-living and living. But if we can make that rigorous.        

Demis Hassabis: 00:47:44: Yes.        

Lex Fridman: 00:47:45: That the very thing from the Big Bang  to today has been the same process. If you can break down that wall that we’ve constructed in our minds of the actual origin from non-living to  living, and it’s not a line that it’s a continuum that connects physics  and chemistry and biology. There’s no line.        

Demis Hassabis: 00:48:03: I mean, this is my whole reason why I  worked on AI and AGI my whole life, because I think it can be the  ultimate tool to help us answer these kinds of questions. And I don’t  really understand why the average person doesn’t worry about this stuff  more. How can we not have a good definition of life and not living a  non-living and the nature of time and let alone consciousness and  gravity and all these things and quantum mechanics weirdness? It’s just  to me, I’ve always had this sort of screaming at me in my face and it’s  getting louder. It’s like, what is going on here? And I mean that in the deeper sense, the nature of reality, which has to be the ultimate  question that would answer all of these things. It’s sort of crazy if  you think about it. We can stare at each other and all these living  things all the time. We can inspect it microscopes and take it apart  almost down to the atomic level. And yet we still can’t answer that  clearly in a simple way. That question of how do you define living? It’s kind of amazing.        

Lex Fridman: 00:49:05: Yeah, living, you can kind of talk  your way out of thinking about. But consciousness, we have this very  obviously subjective, conscious experience like we’re at the center of  our own world and feels like something. And then how are you not  screaming at the mystery of it all? I mean, but really humans have been  contending with the mystery of the world around them for a long, long…  There’s a lot of mysteries like what’s up with the sun and the rain?  What’s that about? And then last year we had a lot of rain, and this  year we don’t have rain. What did we do wrong? Humans have been asking  that question for a long time.        

Demis Hassabis: 00:49:46: Exactly. So I guess we’ve developed a  lot of mechanisms to cope with these deep mysteries that we can’t fully, we can see, but we can’t fully understand and we have to just get on  with daily life. And we keep ourselves busy in a way. In a way, did we  keep ourselves distracted?        

Lex Fridman: 00:50:01: I mean, weather is one of the most  important questions of human history. We still, that’s the go-to small  talk direction of the weather.        

Demis Hassabis: 00:50:09: Yes. Especially in England.        

Lex Fridman: 00:50:11: And then which is famously is an  extremely difficult system to model. And even that system, Google  DeepMind has made progress on.        

Demis Hassabis: 00:50:22: Yes, we’ve created the best weather  prediction systems in the world and they’re better than traditional  fluid dynamics sort of systems that usually calculated on massive  supercomputers takes days to calculate it. And we’ve managed to model a  lot of the weather dynamics with neural network systems, with our  WeatherNet system. And again, it’s interesting that those kinds of  dynamics can be modeled even though very complicated, almost bordering  on chaotic systems in some cases.        

00:50:50: A lot of the interesting aspects of  that can be modeled by these neural network systems, including very  recently we had cyclone prediction of where paths of hurricanes might  go. Of course, super useful, super important for the world and it’s  super important to do that very timely and very quickly and as well as  accurately. And I think it’s very promising direction again, of  simulating so that you can run forward predictions and simulations of  very complicated real world systems.        

Lex Fridman: 00:51:18: I should mention that I’ve gotten a  chance in Texas to meet a community of folks called the Storm Chasers.  And what’s really incredible about them, I need to talk to them more, is they’re extremely tech-savvy because what they have to do is they have  to use models to predict where the storm is. So it’s this beautiful mix  of crazy enough to go into the eye of the storm and in order to protect  your life and predict where the extreme events are going to be, they  have to have increasingly sophisticated models of weather.        

Demis Hassabis: 00:51:50: Yeah.        

Lex Fridman: 00:51:50: It is a beautiful balance of being in  it as living organisms and the cutting edge of science. They actually  might be using DeepMind systems.        

Demis Hassabis: 00:52:01: Yeah. But hopefully they are. And I’d  love to join them in one of those checks. They look amazing. Right.  That’s great to actually experience it one time.        

## Path to AGI

Lex Fridman: 00:52:07: Exactly. And then also to experience  the correct prediction where something will come and how it’s going to  evolve. It’s incredible. You’ve estimated that we’ll have AGI by 2030,  so there’s interesting questions around that. How will we actually know  that we got there and what may be the move quote, “Move 37” of AGI.        

Demis Hassabis: 00:52:33: My estimate is sort of 50% chance by  in the next five years, so by 2030 let’s say. So I think there’s a good  chance that that could happen. Part of it is what is your definition of  AGI? Of course people arguing about that now and mind’s quite a high bar and always has been of can we match the cognitive functions that the  brain has? So we know our brains are pretty much general Turing machines approximate, and of course we created incredible modern civilization  with our minds. So that also speaks to how general the brain is.        

00:53:06: And for us to know we have a true AGI, we would have to make sure that it has all those capabilities. It isn’t kind of a jagged intelligence where some things, it’s really good at,  like today’s systems, but other things it’s really flawed at. And that’s what we currently have with today’s systems. They’re not consistent. So you’d want that consistency of intelligence across the board.        

00:53:28: And then we have some missing, I  think, capabilities like the true invention capabilities and creativity  that we were talking about earlier. So you’d want to see those. How you  test that? I think you just test it. One way to do it would be kind of  brute force test of tens of thousands of cognitive tasks that we know  that humans can do. And maybe also make the system available to a few  hundred of the world’s top experts, the Terence Taos of each subject  area and give them a month or two and see if they can find an obvious  flaw in the system. And if they can’t, then I think you can be pretty  confident we have a fully general system.        

Lex Fridman: 00:54:11: Maybe to push back a little bit, it  seems like humans are really incredible as the intelligence improves  across all domains to take it for granted, like you mentioned, Terence  Tao, these brilliant experts. They might quickly in a span of weeks,  take for granted all the incredible things it can do and then focus in  on, well, aha, right there. I consider myself, first of all, human. I  identify as human. Some people listen to me talk and they’re like, “That guy is not good at talking the stuttering.” So even humans have obvious across domains limits, even just outside of calc, mathematics and  physics and so on. I wonder if it will take something like a Move 37, so on the positive side versus a barrage of 10,000 cognitive tasks where  it’ll be one or two where it’s like, holy shit, this is special.        

Demis Hassabis: 00:55:14: So I think that. Exactly. So I think  there’s the sort of blanket testing to just make sure you’ve got the  consistency. But I think there are the sort of lighthouse moments like  the Move 37 that I would be looking for. So one would be inventing a new conjecture or a new hypothesis about physics like Einstein did.        

00:55:33: So maybe you could even run the back  test of that very rigorously, have a cut-off of 1900 and then give the  system everything that was written up to 1900 and then see if it could  come up with special relativity and general relativity, right? Like  Einstein did. That would be an interesting test. Another one would be  can it invent a game like Go? Go not just come up with Move 37, a new  strategy, but can it invent a game that’s as deep as aesthetically  beautiful, as elegant as Go? And those are the sorts of things I would  be looking out for. And probably a system being able to do several of  those things for it to be very general, not just one domain. And so I  think that would be the signs at least that I would be looking for, that we’ve got a system that’s AGI level and then maybe to fill that out,  you would also check their consistency, make sure there’s no holes in  that system either.        

Lex Fridman: 00:56:27: Yeah, something like a new conjecture or scientific discovery. That would be a cool feeling.        

Demis Hassabis: 00:56:32: Yeah, that would be amazing. So it’s not just helping us do that, but actually coming up with something brand new.        

Lex Fridman: 00:56:38: And you would be in the room for that.        

Demis Hassabis: 00:56:40: Absolutely.        

Lex Fridman: 00:56:40: It would be probably two or three months before announcing it. And you would just be sitting there trying not to Tweet.        

Demis Hassabis: 00:56:49: Something like that. Exactly. It’s  like what is this amazing new physics idea? And then we would probably  check it with world experts in that domain and validate it and go  through its workings. And I guess it would be explaining its workings  too. Yeah. It would be an amazing moment.        

Lex Fridman: 00:57:07: Do you worry that we as humans, even expert humans, like you might miss it? Might miss-        

Demis Hassabis: 00:57:12: Well, it may be pretty complicated. So it could be, the analogy I give there is I don’t think it will be  totally mysterious to the best human scientists, but it may be a bit  like, for example in chess, if I was to talk to Garry Kasparov for  Magnus Carlsen and play a game with them and they make a brilliant move, I might not be able to come up with that move. But they could explain  why afterwards that move made sense. And we would be to understand it to some degree, not to the level they do, but if they were good at  explaining, which is actually part of intelligence too, is being able to explain in a simple way that what you’re thinking about, I think that  that will be very possible for the best human scientists.        

Lex Fridman: 00:57:52: But I wonder, maybe you can educate me on the side of Go, I wonder if there’s moves from Magnus or Garry where they at first will dismiss it as a bad move?        

Demis Hassabis: 00:58:02: Yeah, sure, it could be. But then  afterwards they’ll figure out with their intuition why this works. And  then empirically, the nice thing about games is, one of the great things about games is it’s a sort of scientific test. Do you win the game or  not win? And then that tells you, okay, that move in the end was good,  that strategy was good. And then you can go back and analyze that and  explain even to yourself a little bit more why. Explore around it, and  that’s how chess analysis and things like that works. So perhaps that’s  why my brain works like that because I’ve been doing that since I was  four and it’s sort of hardcore training in that way.        

Lex Fridman: 00:58:39: But even now when I generate code,  there is this kind of nuanced, fascinating contention that’s happening  where I might at first identify as a set of generated code is incorrect  in some interesting nuanced ways. But then I always have to ask the  question, is there a deeper insight here that I’m the one who’s  incorrect? And that’s going to, as the systems get more and more  intelligent, you’re going to have to contend with that. It’s like, is  this a bug or a feature, what you just came up with?        

Demis Hassabis: 00:59:14: Yeah. And they’re going to be pretty  complicated to do, but of course it will be, you can imagine also AI  systems that are producing that code or whatever that is, and then human programmers looking at it, but also not unaided with the help of AI  tools as well. So it’s going to be kind of an interesting, maybe  different AI tools to the ones the monitoring tools are the ones that  generated it.        

Lex Fridman: 00:59:36: So if we look at that AGI system,  sorry to bring it back up, but AlphaEvolve, it’s super cool. So  AlphaEvolve enables on the programming side, something like recursive  self-improvement potentially. If you can imagine what that AGI system,  maybe not the first version, but a few versions beyond that, what does  that actually look like? Do you think it’ll be simple? Do you think  it’ll be something like a self-improving-        

Lex Fridman: 01:00:00: Like, do you think it’ll be simple? Do you think it’ll be something like a self-improving program and a simple one?        

Demis Hassabis: 01:00:06: I mean potentially that’s possible. I  would say I’m not sure it’s even desirable because that’s a kind of hard takeoff scenario. But these current systems like Alpha Evolve, they  have human in the loop deciding on various things, there’re separate  hybrid systems that interact.        

01:00:22: One could imagine eventually doing  that end to end. I don’t see why that wouldn’t be possible, but right  now I think the systems are not good enough to do that in terms of  coming up with the architecture of the code. And again, it’s a little  bit reconnected to this idea of coming up with a new conjectural  hypothesis, how they’re good if you give them very specific instructions about what you’re trying to do, but if you give them a very vague high  level instruction, that wouldn’t work currently. And I think that’s  related to this idea of invent a game as good as Go, right?        

01:00:55: Imagine that was the prompt. That’s  pretty. And so the current systems wouldn’t know I think what to do with that, how to narrow that down to something tractable. And I think  there’s similar, look, just make a better version of yourself. That’s  too unconstrained. But we’ve done it. And as you know with AlphaVol,  like things like faster matrix multiplication, so when you hone it down  to very specific thing you want, it’s very good at incrementally  improving that.        

01:01:21: But at the moment these are more  incremental improvements, sort of small iterations. Whereas if you  wanted a big leap in understanding, you’d need a much larger advance.        

Lex Fridman: 01:01:34: Yeah. But it could also be sort of the pushback against hard takeoff scenario. It could be just a sequence of  incremental improvements, like matrix multiplication. It has to sit  there for days thinking how to incrementally improve a thing and it does solve recursively. And as you do more and more improvement, it’ll slow  down.        

01:01:55: So there be, the path to AGI won’t be like a gradual improvement over time.        

Demis Hassabis: 01:02:03: Yes. If it was just incremental  improvements, that’s how it would look. So the question is, could it  come up with a new leap like the Transformers architecture? Could it  have done that back in 2017 when we did it and Brain did it? And it’s  not clear that these systems, something our AlphaVol wouldn’t be able to do, make such a big leap. So for sure these systems are good. We have  systems I think that can do incremental hill climbing, and that’s a kind of bigger question about is that all that’s needed from here, or do we  actually need one or two more big breakthroughs.        

Lex Fridman: 01:02:34: And can the same kind of systems  provide the breakthroughs also? So make it a bunch of S-curves like  incremental improvement, but also every once in a while, leaps.        

Demis Hassabis: 01:02:44: Yeah, I don’t think anyone has systems that can have shown, unequivocally those big leaps that we have a lot  of systems that do the hill climbing of the S-curve that you’re  currently on.        

Lex Fridman: 01:02:55: And that would be the move 37 is a leap.        

Demis Hassabis: 01:02:59: Yeah, I think it would be a leap, something like that.        

## Scaling laws

Lex Fridman: 01:03:01: Do you think the scaling laws are  holding strong on the pre-training/post-training test time compute? Do  you on the flip side of that, anticipate AI progress hitting a wall?        

Demis Hassabis: 01:03:13: We certainly feel there’s a lot more  room just in the scaling. So actually all steps pre-training,  post-training, and inference time. So there’s sort of three scalings  that are happening concurrently. And again there, it’s about how  innovative you can be and we pride ourselves on having the broadest and  deepest research bench. We have amazing, incredible researchers and  people like Noam Shazir who came up with Transformers and Dave Silver  who led the AlphaGo project and so on.        

01:03:47: And that research base means that if  some new breakthrough is required, like an AlphaGo or Transformers, I  would back us to be the place that does that. So I’m actually quite like it when the terrain gets harder, right? Because then it veers more from just engineering to true research, and research plus engineering, and  that’s our sweet spot and I think that’s harder. It’s harder to invent  things than to fast follow.        

01:04:17: And so we don’t know, I would say it’s kind of 50/50 whether new things are needed or whether the scaling the  existing stuff is going to be enough. And so, in true kind of empirical  fashion, we are pushing both of those as hard as possible. The new blue  sky, ideas and maybe about half our resources are on that. And then  scaling to the max, the current capabilities. And we’re still seeing  some fantastic progress on each different version of Gemini.        

Lex Fridman: 01:04:48: That’s interesting the way you put it  in terms of the deep bench, that if progress towards AGI is more than  just scaling compute, so the engineering side of the problem, and is  more on the scientific side where there’s breakthroughs needed, then you feel confident DeepMind as well, Google DeepMind as well positioned to  kick ass in that domain.        

Demis Hassabis: 01:05:13: Well, I mean if you look at the  history of the last decade or 15 years, it’s been maybe, I don’t know,  80-90% of the breakthroughs that underpins modern AI field today was  from originally Google Brain, Google Research and DeepMind. So yeah, I  would back that to continue hopefully.        

Lex Fridman: 01:05:31: So on the data side, are you concerned about running out of high quality data, especially high quality human data?        

Demis Hassabis: 01:05:37: I’m not very worried about that.  Partly because I think there’s enough data, and it’s been proven to get  the systems to be pretty good. And this goes back to simulations again.  Do you have enough data to make simulations, so that you can create more synthetic data that are from the right distribution? Obviously that’s  the key. So you need enough real-world data in order to be able to  create those kinds of data generators, and I think that we’re at that  step at the moment.        

Lex Fridman: 01:06:05: Yeah, you’ve done a lot of incredible stuff on the side of science and biology, doing a lot with not so much data.        

Demis Hassabis: 01:06:12: Yeah.        

Lex Fridman: 01:06:12: I mean it’s still a lot of data, but I guess enough to-        

Demis Hassabis: 01:06:15: To get that going. Exactly. Exactly        

## Compute

Lex Fridman: 01:06:18: Yeah. How crucial is the scaling of  compute to building AGI? That’s an engineering question. It’s almost a  geopolitical question because it also integrated into that is supply  chains and energy. A thing that you care a lot about, which is  potentially fusion. So innovating on the side of energy also. Do you  think we’re going to keep scaling compute?        

Demis Hassabis: 01:06:42: I think so, for several reasons. I  think compute, there’s the amount of compute you have for training,  often it needs to be co-located, so actually even bandwidth constraints  between data centers can affect that. So there’s additional constraints  even there and that’s important for training, obviously the largest  models you can, but there’s also because now AI systems are in products  and being used by billions of people around the world, you need a ton of inference compute now.        

01:07:10: And then on top of that there’s the  thinking systems, the new paradigm of the last year that where they get  smarter, the longer amount of inference time you give them at test time. So all of those things need a lot of compute and I don’t really see  that slowing down, and as AI systems become better, they’ll become more  useful and there’ll be more demand for them. So both from the training  side, the training side actually is only just one part of that. It may  even become the smaller part of what’s needed in the overall compute  that’s required.        

Lex Fridman: 01:07:42: Yeah, that’s one sort of almost meme-y kind of thing, which is the success in the incredible aspects of VL3.  People kind of make fun of the more successful it becomes, the servers  are sweating.        

Demis Hassabis: 01:07:55: Yes.        

Lex Fridman: 01:07:55: The inference.        

Demis Hassabis: 01:07:57: Yeah, yeah, exactly. We did a little  video of the servers frying eggs and things. That’s right. And we are  going to have to figure out how to do that. There’s a lot of interesting hardware innovations that we do as we have our own TPU line and we’re  looking at inference-only things, inference-only chips and how we can  make those more efficient.        

01:08:15: We’re also very interested in building AI systems and we have done the help with energy usage, so help data  center energy like for the cooling systems be efficient, grid  optimization, and then eventually things like helping with  plasma-containment fusion reactors. We’ve done lots of work on that with Commonwealth Fusion, and also one could imagine reactor design.        

01:08:38: And then material design I think is  one of the most exciting. New types of solar material, solar panel  material room temperature superconductors has always been on my list of  dream breakthroughs, and optimal batteries. And I think a solution to  any one of those things would be absolutely revolutionary for climate  and energy usage. And we’re probably close, and again in the next five  years to having AI systems that can materially help with those problems.        

## Future of energy

Lex Fridman: 01:09:05: If you were to bet, sorry for the  ridiculous question, but what is the main source of energy in 20, 30, 40 years. Do you think it’s going to be nuclear fusion?        

Demis Hassabis: 01:09:15: I think fusion and solar are the two  that I would bet on. Solar, I mean it’s the fusion reactor in the sky of course, and I think really the problem there is batteries and  transmission. So as well as more efficient, more and more efficient  solar material perhaps eventually in space, these kind of Dyson Sphere  type ideas.        

01:09:36: And fusion I think is definitely  doable, it seems, if we have the right design of reactor and we can  control the plasma and fast enough and so on, and I think both of those  things will actually get solved. So we’ll probably have at least those  are probably the two primary sources of renewable, clean, almost free or perhaps free energy.        

Lex Fridman: 01:09:58: What a time to be alive. If I traveled into the future with you a hundred years from now, how much would you  be surprised if we’ve passed a type one Kardashev scale civilization?        

Demis Hassabis: 01:10:11: I would not be that surprised if it  was a hundred-year timescale from here. I mean I think it’s pretty clear if we crack the energy problems in one of the ways we’ve just discussed or very efficient solar, then if energy is kind of free and renewable  and clean, then that solves a whole bunch of other problems.        

01:10:32: So for example, the water access  problem goes away because you can just use desalination. We have the  technology, it’s just too expensive. So only fairly wealthy countries  like Singapore and Israel and so on actually use it. But if it was  cheap, then all countries that have a coast could, but also you’d have  unlimited rocket fuel. You could just separate seawater out into  hydrogen and oxygen using energy and that’s rocket fuel.        

01:10:57: So combined with Elon’s, amazing self  landing rockets, then it could be you sort of like a bus service to  space. So that opens up incredible new resources and domains. Asteroid  mining I think will become a thing, and maximum human flourishing to the stars. That’s what I dream about as well is like Carl Sagan’s sort of  idea of bringing consciousness to the universe, waking up the universe.  And I think human civilization will do that in the full sense of time if we get AI right, and crack some of these problems with it.        

Lex Fridman: 01:11:30: Yeah, I wonder what it would look like if you’re just a tourist flying through space. You would probably  notice earth because if you solve the energy problem, you would see a  lot of space rockets probably. So it would be traffic here in London,  but in space.        

Demis Hassabis: 01:11:46: Yes, exactly.        

Lex Fridman: 01:11:46: It’s just a lot of rockets. And then  you would probably see floating in space, some kind of source of energy  like solar potentially. So earth would just look more on the surface,  more technological. And then you would use the power of that energy then to preserve the natural…        

Demis Hassabis: 01:12:05: Yes.        

Lex Fridman: 01:12:06: Like the rainforest and all that kind of stuff.        

Demis Hassabis: 01:12:07: Exactly. Because for the first time in human history we wouldn’t be resource constrained. And I think that  could be amazing new era for humanity where it’s not zero-sum, right? I  have this land, you don’t have it. Or if the tigers have their forest,  then the local villages can’t, what are they going to use? I think that  this will help a lot. No, it won’t solve all problems because there’s  still other human foibles that will still exist, but it will at least  remove one, I think one of the big vectors, which is scarcity of  resources, including land and more materials and energy.        

01:12:45: And we should be sometimes call it  another call about this kind of radical abundance era, where there’s  plenty of resources to go around. Of course the next big question is  making sure that that’s fairly, shared fairly and everyone in society  benefits from that.        

## Human nature

Lex Fridman: 01:13:01: So there is something about human  nature where I go, its like Borat, like my neighbor. You start trouble.  We do start conflicts and that’s why games throughout, as I’m learning  actually more and more, even in ancient history, serve the purpose of  pushing people away from war, actually hot war. So maybe we can figure  out increasingly sophisticated video games that pull us, they give us  that… Scratch the itch of conflict, whatever that is, but us, the human  nature.        

Demis Hassabis: 01:13:38: Like… Yeah.        

Lex Fridman: 01:13:38: And then avoid the actual hot wars  that would come with increasingly sophisticated technologies because  we’re now, we’ve long passed the stage where the weapons we’re able to  create can actually just destroy all of human civilization. So that’s no longer a great way to start with your neighbor. It’s better to play a  game of chess.        

Demis Hassabis: 01:14:03: Or football.        

Lex Fridman: 01:14:03: Or football. Yeah.        

Demis Hassabis: 01:14:05: And I think that’s what my modern  sport is and I love football watching it and I just feel like, and I  used to play it a lot as well, and it’s very visceral in its tribal, and I think it does channel a lot of those energies into which I think is a kind of human need to belong to some group, but into a fun way, a  healthy way and not destructive way, kind of constructive thing.        

01:14:33: And I think going back to games again  is I think they’re originally why they’re so great as well for kids to  play things like chess is they’re great little microcosm simulations of  the world. They’re simulations of the world too. They’re simplified  versions of some real world situation, whether it’s poker or Go or  chess, different aspects or diplomacy, different of the real world.        

01:14:53: And it allows you to practice at them  too, because how many times do you get to practice a massive decision  moment in your life? What job to take, what university to go to? You get maybe, I don’t know, a dozen or so key decisions one has to make and  you’ve got to make those as best as you can. And games is a kind of safe environment, repeatable environment where you can get better at your  decision-making process, and it maybe has this additional benefit of  channeling some energies into more creative and constructive pursuits.        

Lex Fridman: 01:15:24: Well I think it’s also really important to practice losing and winning.        

Demis Hassabis: 01:15:28: Right.        

Lex Fridman: 01:15:29: Losing is a really, that’s why I love  games. That’s why I love even things like Brazilian Jiu-Jitsu where you  can get your kicked in a safe environment over and over. It reminds you  about physics, about the way the world works about sometimes you lose,  sometimes you win, you can still be friends with everybody. But that  feeling of losing, I mean it’s a weird one for us humans to really make  sense of. That’s just part of life. That is a fundamental part of life  is losing.        

Demis Hassabis: 01:16:00: And I think the martial arts as I  understand it, but also in things like light chess is at least the way I took it’s a lot to do with self-improvement, self-knowledge. That,  okay, so I did this thing. It’s not about really beating the other  person, it’s about maximizing your own potential.        

01:16:16: If you do it in a healthy way, you  learn to use victory and losses in a way. Don’t get carried away with  victory and think you’re just the best in the world. And the losses keep you humble, and always knowing there’s always something more to learn.  There’s always a bigger expert that you can mentor you. I think you  learn that I’m pretty sure in martial arts.        

01:16:35: And I think that’s also the way that  at least I was trained in chess. And so, in the same way, and it can be  very hardcore and very important and of course you want to win, but you  also need to learn how to deal with setbacks in a healthy way, and wire  that feeling that you have when you lose something into a constructive  thing of, next time I’m going to improve this or get better at this.        

Lex Fridman: 01:16:57: There is something that’s a source of  happiness, a source of meaning that improvements that… It’s not about  the winning or losing.        

Demis Hassabis: 01:17:04: Yes, the mastery. There’s nothing more satisfying in a way. It’s like, oh wow, this thing I couldn’t do  before. Now I can. And again, games and physical sports and mental  sports, their ways of measuring their beautiful, because you can measure that progress.        

Lex Fridman: 01:17:19: Yeah, there’s something about I guess  why I love role-playing games, like the number go up of on the skill  tree, literally that is a source of meaning for us humans, whatever our-        

Demis Hassabis: 01:17:29: Yeah, we’re quite addicted to this  sort of, these numbers going up. And maybe that’s why we made games like that because obviously that is something we’re hill climbing systems  ourselves, right?        

Lex Fridman: 01:17:42: Yeah. It would be quite sad if we didn’t have any mechanism-        

Demis Hassabis: 01:17:45: Color belts, we do this everywhere, where we just have thing that…        

Lex Fridman: 01:17:51: And I don’t want to dismiss that. There is a source of deep meaning across humans.        

## Google and the race to AGI

01:17:55: So one of the incredible stories on  the business, on the leadership side is what Google has done over the  past year. So I think it’s fair to say that Google was losing on the LLM product side a year ago with Gemini 1.5 And now it’s winning, which…  I’m Joe Biden. And you took the helm and you led this effort. What did  it take to go from let’s say quote-unquote losing to quote-unquote  winning, in the span of a year?        

Demis Hassabis: 01:18:22: Yeah, well firstly it’s absolutely  incredible team that we have led by Corey and Jeff Dean and Oriole and  the amazing team we have on Gemini. Absolutely. So you can’t do it  without the best talent. And of course we have a lot of great compute as well. But then it’s the research culture we’ve created and basically  coming together both different groups in Google that was Google Brain,  World-class team, and then the old DeepMind, and pulling together all  the best people and the best ideas and gathering around to make the  absolute greater system we could.        

01:18:59: And it was been hard, but we’re all  very competitive and we love research. This is so fun to do, and it’s  great to see our trajectory. It wasn’t a given, but we’re very pleased  with where we are and the rate of progress is the most important thing.  So if you look at where we’ve come to from two years ago to one year ago to now, I think we call it relentless progress. Along with relentless  shipping of that progress is being very successful and it’s unbelievably competitive, the whole space, the whole AI space, with some of the  greatest entrepreneurs and leaders and companies in the world, all  competing now because everyone’s realized how important AI is. And it’s  very been pleasing for us to see that progress.        

Lex Fridman: 01:19:47: Google’s a gigantic company. Can you  speak to the natural things that happen in that case is the bureaucracy  that emerges? You want to be careful the natural, there’s meetings and  there’s managers and that. What are some of the challenges from a  leadership perspective, breaking through that in order to, like you  said, ship? Like the number of products, Gemini related products that  has been shipped over the past years is insane.        

Demis Hassabis: 01:20:14: Right? Yeah, exactly. That’s what  relentlessness looks like. I think it’s a question of any big company  ends up having a lot of layers of management and things like that is  sort of the nature of how it works. But I still operate and I was always operating with old DeepMind as a start-up still. A large one, but still as a start-up.        

01:20:37: And that’s what we still act like  today with Google DeepMind. And acting with decisiveness and the energy  that you get from the best smaller organizations. And we try to get the  best of both worlds where we have this incredible, billions of users  surfaces and credible products that we can power up with our AI and our  research and that’s amazing and that’s very few places in the world you  can get that, do incredible world-class research on the one hand and  then plug it in and improve billions of people’s lives the next day.  That’s a pretty amazing combination.        

01:21:10: And we’re continually fighting and  cutting away bureaucracy to allow the research culture and the  relentless shipping culture to flourish. And I think we’ve got a pretty  good balance, whilst being responsible with it, as you have to be as a  large company and also with a number of huge product surfaces that we  have.        

Lex Fridman: 01:21:30: So a funny thing you mentioned about  the surface with the billion, I had a conversation with a guy named,  brilliant guy here at the British Museum, called Irvin Finkel. He’s a  world expert at cuneiforms, which is a ancient writing on tablets and he doesn’t know about ChatGPT or Gemini, he doesn’t even know about AI,  but this first encounter with this AI is AI mode on Google.        

Demis Hassabis: 01:21:57: Yes.        

Lex Fridman: 01:21:58: He’s like, is that what you’re talking about, this AI mode? And it’s just a reminder that there’s a large part of the world that doesn’t know about this AI thing.        

Demis Hassabis: 01:22:08: Yeah, I know. It’s funny. If you live  on X and Twitter and I mean it’s sort of at least my feed, it’s all AI.  And there’s certain places where in the valley and certain pockets where everyone’s just, all they’re thinking about is AI, but a lot of the  normal world hasn’t come across it yet.        

Lex Fridman: 01:22:24: And that’s a great responsibility to  their first interaction. The grand scale of the rural, India or anywhere across the world you get to…        

Demis Hassabis: 01:22:34: And we want it to be as good as  possible and in a lot of cases it’s just under the hood powering, making something like maps or search work better. And ideally for a lot of  those people should just be seamless. It’s just new technology that  makes their lives more productive and helps them.        

Lex Fridman: 01:22:50: A bunch of folks on the Gemini product and engineering teams spoken extremely highly of you on another  dimension, that I almost didn’t even expect. I kind of think of you as  the deep scientists and caring about these big research scientific  questions. But they also said you’re a great product guy, like how to  create a thing that a lot of people would use and enjoy using. So can  you maybe speak to what it takes to create a AI based product that a lot of people enjoy using?        

Demis Hassabis: 01:23:18: Yeah. Well, I mean, again, that comes  back from my game design days where I used to design games for millions  of gamers. People would forget about that. I’ve had experience with  cutting edge technology in product that is how games was in the  nineties.        

01:23:31: And so I love actually the combination of cutting edge research and then being applied in a product and to  power a new experience. And so, I think it’s the same skill really of  imagining what it would be like to use it viscerally, and having good  taste coming back to earlier. The same thing that’s useful in science, I think can also be useful in product design.        

01:23:57: And I’ve just had a very, always been a sort of multidisciplinary person, so I don’t see the boundaries really  between arts and sciences, or product and research. It’s a continuum for me. I like working on products that are cutting edge. I wouldn’t be  able to have cutting edge technology under the hood. I wouldn’t be  excited about them if they were just run-of-the-mill products. It  requires this invention, creativity, cap capability.        

Lex Fridman: 01:24:23: What are some specific things you  learned about when you, even on the LLM side, you’re interacting with  Gemini? This doesn’t feel like, the layout, the interface, maybe the  trade-off between the latency, how to present to the user, how long to  wait and how that waiting is shown or the reason capabilities. There are some interesting things because like you said, it’s the very cutting  edge. We don’t know how to present it correctly. So is there some  specific things you’ve learned?        

Demis Hassabis: 01:24:55: I mean it’s such a false evolving  space, evaluating this all the time, but where we are today is that you  want to continually simplify things, whether that’s the interface or  what you build on top of the model, you kind of want to get out of the  way of the model. The model train is coming down the track and it’s  improving unbelievably fast. This relentless progress we talked about  earlier.        

01:25:17: You look at 2.5 versus 1.5 and it’s  just a gigantic improvement, and we expect that again for the future  versions. And so the models are becoming more capable.        

01:25:26: So you’ve got, the interesting thing  about the design space in today’s world, these AI first products is  you’ve got to design not for what the thing can do today, the technology can do today, but in a year’s time. So you actually have to be a very  technical product person, because you’ve got to have a good intuition  for and feel for, okay, that thing that I’m dreaming about now can’t be  done today, but is the research track on schedule to basically intercept that in six months or a year’s time.        

01:25:55: So you’ve kind of got to intercept  where this highly changing technology’s going, as well as the new  capabilities are coming online all the time that we didn’t realize  before that can allow these research to work. Or now we’ve got video  generation, what do we do with that, this multimodal stuff.        

01:26:13: Is it, one question I have is it  really going to be the current UI that we have today, these text box  chats? Seems very unlikely once you think about these super multimodal  systems. Shouldn’t it be something more like Minority Report where you  are sort of vibing with it in a kind of collaborative way? It seems very restricted today. I think we’ll look back on today’s interfaces and  products and systems as quite archaic in maybe in just a couple of  years.        

01:26:41: So I think there’s a lot of space actually for innovation to happen on the product side as well as the research side.        

Lex Fridman: 01:26:47: And then we are offline talking about  the keyboard is, the open question is how, when and how much will we  move to audio as the primary way of interacting with the machines around us versus typing stuff?        

Demis Hassabis: 01:27:00: Yeah, I mean typing is a very low  bandwidth way of doing it, even if you’re a very fast typer. And I think we’re going to have to start utilizing other devices, whether that’s  smart glasses, audio earbuds, and eventually maybe some sorts of neural  devices, where we can increase the input and the output bandwidth to  something maybe a 100x of what is today.        

Lex Fridman: 01:27:24: I think that underappreciated art form is the interface design because I think you can not unlock the power of the intelligence of a system if you don’t have the right interface. The interface is really the way you unlock its power. It’s such an  interesting question of how to do that. So how you would think getting  out of the way isn’t real art form.        

Demis Hassabis: 01:27:46: Yes. It’s the sort of thing that I  guess Steve Jobs always talked about, right? It’s simplicity, beauty,  and elegance that we want. And we’re not that nobody’s there yet, in my  opinion. And that’s what I would like us to get to.        

01:27:58: Again, it sort of speaks to Go again  as a game, the most elegant, beautiful game. Can you make an interface  as beautiful as that? Actually, I think we’re going to enter an era of  AI-generated interfaces that are probably personalized to you, so it  fits the way that you, your aesthetic, your feel, the way that your  brain works and the AI kind of generates that depending on the task.  That feels like that’s probably the direction we’ll end up in.        

Lex Fridman: 01:28:25: Because some people are power users  and they want every single parameter on the screen, everything based  perhaps me with a keyboard-based navigation and to have shortcuts for  everything. And some people like the minimalism.        

Demis Hassabis: 01:28:37: Just hide all of that complexity. Yeah, exactly.        

Lex Fridman: 01:28:39: Yeah. Well, I’m glad you have a Steve Jobs mode in you as well. This is great. Einstein mode, Steve Jobs mode.        

01:28:47: All right, let me try to trick you  into answering a question. When will Gemini 3 come up? Is it before or  after DTS-6? The world waits for both.        

01:28:56: And what does it take to go from 2.5  To 3.0? Because it seems like there’s been a lot of releases of 2.5,  which are already leaps in performance. So what does it even mean to go  to a new version? Is it about performance? Is it about a completely  different flavor of an experience?        

Demis Hassabis: 01:29:16: Yeah, well, so the way it works with  our different version numbers is we try to collect, so maybe it takes  roughly six months or something to do a new kind of full run and the  full productization of a new version.        

01:29:32: And during that time, lots of new  interesting research iterations and ideas come up, and we sort of  collect them all together that you could imagine the last six months  worth of interesting ideas on the architecture front, maybe it’s on the  data front, it’s like many different possible things. And we package  that all up, test which ones are likely to be useful for the next  iteration, and then bundle that all together. And then we start the new  giant hero training run. And then of course that gets monitored.        

Demis Hassabis: 01:30:00: … run, right? And then of course that  gets monitored and then at the end of the pre-training, then there’s all the post-training, there’s many different ways of doing that, different ways of patching it. So there’s a whole experimental phase there which  you can also get a lot of gains out. And that’s where you see the  version numbers usually referring to the base model, the pre-trained  model, and then the interim versions of 2.5 and the different sizes and  the different little additions. They’re often patches or post-training  ideas that can be done afterwards off the same basic architecture. And  then of course on top of that, we also have different sizes, Pro and  Flash and Flashlight that are often distilled from the biggest ones, the Flash model from the Pro model. And that means we have a range of  different choices. If you’re the developer, do you want to prioritize  performance or speed and cost?        

01:30:51: And we like to think of this Pareto  frontier of on the one hand, the Y-axis is like performance, and then  the X- axis is cost or latency and speed basically. And we have models  that completely define the frontier. So whatever your trade-off is that  you want as an individual user or as a developer, you should find one of our models satisfies that constraint.        

Lex Fridman: 01:31:17: So behind the version changes, there  is a big run and then there’s just an insane complexity of  productization. Then there’s the distillation of the different sizes  along that Pareto front. And then as with each step you take, you  realize there might be a cool product. There’s side quests.        

Demis Hassabis: 01:31:39: Yes, exactly.        

Lex Fridman: 01:31:41: And then you also don’t want to take too many side quests because then you have a million versions and a million products.        

Demis Hassabis: 01:31:45: Yes, precisely.        

Lex Fridman: 01:31:46: It’s very unclear, but you also get  super excited because it’s super cool. How does even look at VLs? Very  cool. How does it fit into the bigger Thing?        

Demis Hassabis: 01:31:55: Yes, exactly. Exactly. And then you’re constantly this process of converging upstream, we call it ideas from  the product surfaces or from the post-training and even further  downstream and that, you upstream that into the core model training for  the next run. So then the main model, the main Gemini track becomes more and more general and eventually, AGI.        

Lex Fridman: 01:32:20: One hero run.        

Demis Hassabis: 01:32:21: Yes, exactly. A few hero runs later.        

Lex Fridman: 01:32:23: Yeah. So sometimes when you release  these new versions or every version, really, are benchmarks productive  or counterproductive for showing the performance of a model?        

Demis Hassabis: 01:32:36: You need them, but it’s important that you don’t overfit to them. So they shouldn’t be the be all and end all. So there’s LMArena, or it used to be called LEMSYS, that’s one of them  that turned out organically to be one of the main ways people like to  test these systems, at least the chatbots. Obviously there’s loads of  academic benchmarks that test mathematics and coding ability, general  language ability, science ability and so on. And then we have our own  internal benchmarks that we care about.        

01:33:04: It’s a multi objective optimization  problem. You don’t want to be good at just one thing. We’re trying to  build general systems that are good across the board, and you try and  make no-regret improvements. So where you improve in coding, but it  doesn’t reduce your performance in other areas. So that’s the hard part  because of course you could put more coding data in or you could put  more, I don’t know, gaming data in, but then does it make worse your  language system or your translation systems and other things that you  care about? So you’ve got to continually monitor this increasingly  larger and larger suite of benchmarks. And also when you stick them into products, these models, you also care about the direct usage and the  direct stats and the signals that you’re getting from the end users,  whether they’re coders or the average person using the chat interfaces.        

Lex Fridman: 01:34:00: Because ultimately, you want to  measure the usefulness, but it’s so hard to convert that into a number.  It’s really vibe based benchmarks across a large number of users. And  it’s hard to know and it would be just terrifying to me, you know have a much smarter model, but it’s just something vibe based. It’s not quite  working. That’s such a scary and everything you just said. It has to be  smart and useful across so many domains. So you get super excited all of a sudden solving programming problems you’ve never been able to solve  before, but now it’s crappier poetry or something and it’s just, I don’t know, that’s a stressful. That’s so difficult-        

Demis Hassabis: 01:34:43: To balance.        

Lex Fridman: 01:34:44: To balance and because you can’t really trust the benchmarks, you really have to trust the end users.        

Demis Hassabis: 01:34:48: Yeah. And then other things that are  even more esoteric come into play, like the style of the persona of the  system, is it verbose? Is it succinct? Is it humorous? And different  people like different things. So it’s very interesting. It’s almost like cutting edge part of psychology research or personality research. I  used to do that in my PhD, like five factor personality, what do we  actually want our systems to be like? And different people will like  different things as well. So these are all just new problems in product  space that I don’t think I’ve ever really been tackled before, but we’re going to rapidly have to deal with now.        

Lex Fridman: 01:35:27: I think it’s a super fascinating  space, developing the character of the thing and in so doing, it puts a  mirror to ourselves, what are the kind of things that we like? Because  prompt engineering allows you to control a lot of those elements, but  can the product make it easier for you to control the different flavors  of those experiences, the different characters that you interact with?        

Demis Hassabis: 01:35:51: Yeah, exactly.        

## Competition and AI talent

Lex Fridman: 01:35:52: So what’s the probability of Google DeepMind winning?        

Demis Hassabis: 01:35:56: Well, I see it as winning. I think  winning is the wrong way to look at it given how important and  consequential what it is we’re building. So funny enough, I try not to  view it like a game or competition even though that’s a lot of my  mindset. It’s about in my view, all of us or those of us at the leading  edge or have a responsibility to steward this unbelievable technology  that could be used for incredible good but also has risks, steward it  safely into the world for the benefit of humanity. That’s always what  I’ve dreamed about and what we’ve always tried to do. And I hope that’s  what eventually the community, maybe the international community will  rally around when it becomes obvious that as we get closer and closer to AGI, that’s what’s needed.        

Lex Fridman: 01:36:43: I agree with you. I think that’s  beautifully put. You’ve said that you talk to and are on good terms with the leads of some of these labs. As the competition heats up, how hard  is it to maintain those relationships?        

Demis Hassabis: 01:36:58: It’s been okay so far. I try to pride  myself in being collaborative. I’m a collaborative person. Research is a collaborative endeavor. Science is a collaborative endeavor. It’s all  good for humanity in the end if you cure terrible diseases and you come  up with an incredible cure, this is net win for humanity. And the same  with energy, all of the things that I’m interested in helping solve with AI. So I just want that technology to exist in the world and be used  for the right things and the benefits of that, the productivity benefits of that being shared for the benefit of everyone. So I try to maintain  good relations with all the leading lab people. They’re very interesting characters, many of them as you might expect.        

Lex Fridman: 01:37:40: Yep.        

Demis Hassabis: 01:37:41: But yeah, I’m on good terms I hope  with pretty much all of them. And I think that’s going to be important  when things get even more serious than they are now, that there are  those communication channels and that’s what will facilitate cooperation or collaboration if that’s what is required, especially on things like  safety.        

Lex Fridman: 01:38:00: Yeah, I hope there’s some  collaboration on stuff that’s less high stakes and in so doing, serves  as a mechanism for maintaining friendships and relationships. So for  example, I think the internet would love it if you and Elon somehow  collaborate on creating a video game, that kind of thing. I think that  enables camaraderie and good terms. And also you two are legit gamers,  so it’s just fun to to create some-        

Demis Hassabis: 01:38:22: Yeah, that would be awesome. And we’ve talked about that in the past and it may be a cool thing that we can  do. And I agree with you, it’d be nice to have side projects in a way  where one can just lean into the collaboration aspect of it and it’s a a win-win for both sides and it builds up that collaborative muscle.        

Lex Fridman: 01:38:44: I see the scientific endeavor as that  side project for humanity and I think Google DeepMind has been really  pushing that. I would love to see other labs do more scientific stuff  and then collaborate because it just seems like it’s easier to  collaborate on the big scientific questions.        

Demis Hassabis: 01:39:01: I agree and I would love to see a lot  of people, all of the other labs talk about science, but I think we’re  really the only ones using it for science and doing that. And that’s why projects like AlphaFold are so important to me. And I think to our  mission is to show how AI can be clearly used in a very concrete way for the benefit of humanity. And also, we spun out companies like  Isomorphic off the back of Alpha Fold to do drug discovery and it’s  going really well and you can think of build additional AlphaFold type  systems to go into chemistry space to help accelerate drug design. And  the examples I think we need to show and society needs to understand are where AI can bring these huge benefits.        

Lex Fridman: 01:39:42: Well, from the bottom of my heart,  thank you for pushing the scientific efforts forward with rigor, with  fun, with humility, all of it. I just love to see and still talking  about P equals NP, it’s just incredible. So I love it. There’s been  seemingly a war for talent. Some of it is meme, I don’t know. What do  you think about Meta buying up talent with huge salaries and the heating up of this battle for talent? I should say that I think a lot of people see DeepMind as a really great place to do cutting-edge work for the  reasons that you’ve outlined. There’s this vibrant scientific culture.        

Demis Hassabis: 01:40:21: Yeah. Well look, of course there’s a  strategy that Meta is taking right now. I think that from my perspective at least, I think the people that are real believers in the mission of  AGI and what it can do and understand the real consequences, both good  and bad from that and what that responsibility entails, I think they’re  mostly doing it to be like myself, to be on the frontier of that  research so they can help influence the way that goes and steward that  technology safely into the world. And Meta right now are not at the  frontier. Maybe they’ll manage to get back on there and it’s probably  rational what they’re doing from their perspective because they’re  behind and they need to do something. But I think there’s more important things than just money. Of course one has to pay people their market  rates and all of these things and that continues to go up. And I was  expecting this because more and more people are finally realizing,  leaders of companies, what I’ve always known for 30 plus years now,  which is that AGI is the most important technology probably that’s ever  going to be invented. So in some senses, it’s rational to be doing that. But I also think there’s a much bigger question. People in AI these  days are very well paid.        

01:41:32: I remember when we were starting out  back in 2010, I didn’t even pay myself a couple of years because it  wasn’t enough money. We couldn’t raise any money, and these days,  interns are being paid the amount that we raised as our first entire  seed round. So it’s pretty funny. And I remember the days where I used  to have to work for free and almost pay my own way to do an internship.  Right now, it’s all the other around, but that’s just how it is. It’s  the new world. But I think that we’ve been discussing what happens post- AGI and energy systems are solved and so on, what is even money going  to mean? So I think in the economy and we’re going to have much bigger  issues to work through and how does the economy function in that world  and companies? So I think it’s a little bit of a side issue about  salaries and things like that today.        

Lex Fridman: 01:42:19: Yeah, when you’re facing such gigantic consequences and gigantic, fascinating scientific questions-        

Demis Hassabis: 01:42:25: Which may be only a few years away.        

## Future of programming

Lex Fridman: 01:42:27: So the practicals, the pragmatic  sense, if we zoom in on jobs, we can look at programmers because it  seems like AI systems are currently doing incredibly well at programming and increasingly so. So A lot of people that program for a living, love programming are worried they will lose their jobs. How worried should  they be do you think, and what’s the right way to adjust to the new  reality and ensure that you survive and thrive as a human in the  programming world?        

Demis Hassabis: 01:42:58: Well, it’s interesting that  programming, and it’s again counterintuitive to what we thought years  ago, maybe that some of the skills that we think of as harder skills are turned out maybe to be the easier ones for various reasons. But coding  and maths, because you can create a lot of synthetic data and verify if  that data’s correct. So because of that nature of that, it’s easier to  make things like synthetic data to train from. It’s also an area of  course we’re all interested in because as programmers to help us and get faster at it and more productive.        

01:43:27: So I think for the next era, like the  next five, 10 years, I think what we’re going to find is people who  embrace these technologies become almost at one with them, whether  that’s in the creative industries or the technical industries will  become superhumanly productive, I think. So the great programmers will  be even better, but there’ll be even 10X even what they are today. And  because there, you’ll be able to use their skills to utilize the tools  to the maximum, exploit them to the maximum. And so I think that’s what  we’re going to see in the next domain. So that’s going to cause quite a  lot of change. And so that’s coming. A lot of people benefit from that.        

01:44:05: So I think one example of that is if  coding becomes easier, it becomes available to many more creatives to do more. But I think the top programmers will still have huge advantages  as terms of specifying, going back to specifying what the architecture  should be. The question should be how to guide these coding assistants  in a way that’s useful and check whether the code they produce is good.  So I think there’s plenty of headroom there for the foreseeable next few years.        

Lex Fridman: 01:44:36: So I think there’s several interesting things there. One is there’s a lot of imperative to just get better and better consistently of using these tools so they’re riding the wave of  the improving models versus competing against them. But sadly, but  that’s the nature of life on earth, there could be a huge amount of  value to certain kinds of programming at the cutting edge and less value to other kinds. For example, it could be front-end web design might be  more amenable to, as you’ve mentioned, to generation by AI systems and  maybe for example, game engine design or something like this or back-end design or guiding systems in high-performance situations,  high-performance programming type of design decisions, that might be  extremely valuable. But it will shift where the humans are needed most  and that’s scary for people to address.        

Demis Hassabis: 01:45:37: Yeah, I think that’s right. Anytime  where there’s a lot of disruption and change, and we’ve had this, it’s  not just this time. We’ve had this in many times in human history with  the internet, mobile, but before that obviously, the Industrial  Revolution and it’s going to be one of those eras where there will be a  lot of change. I think there’ll be new jobs we can’t even imagine today, just like the internet created. And then those people with the right  skill sets to ride that wave will become incredibly valuable, those  skills. But maybe people will have to relearn or adapt a bit, their  current skills. And the thing that’s going to be harder to deal with  this time around is that I think what we’re going to see is something  like probably 10 times the impact the Industrial Revolution had, but 10  times faster as well. So instead of a 100 years, it takes 10 years and  so that’s going to make, it’s like a 100X, the impact and the speed  combined.        

01:46:31: So I think going to make it more  difficult for society to deal with and there’s a lot to think through  and I think we need to be discussing that right now. And I encourage top economists in the world and philosophers to start thinking about how is society going to be affected by this and what should we do? Including  things like universal basic provision or something like that where a lot of the increased productivity gets shared out and distributed to  society and maybe in the form of services and other things where if you  want more than that, you still go and get some incredibly rare skills  and things like that and make yourself unique. But there’s a basic  provision that is provided.        

Lex Fridman: 01:47:19: And if you think of government as a  technology, there’s also interesting questions, not just in the  economics, but just politics. How do you design a system that’s  responding to the rapidly changing times such that you can represent the different pain that people feel from the different groups and how do  you reallocate resources in a way that addresses that pain and  represents the hope and the pain and the fears of different people in a  way that doesn’t lead to division? Because politicians are often really  good at fueling the division and using that to get elected, defining the other and then saying that’s bad. And based on that, I think that’s  often counterproductive to leveraging a rapidly changing technology to  help the world flourish. So we almost need to improve our political  systems as well rapidly, if you think of them as a technology.        

Demis Hassabis: 01:48:19: Definitely. And I think we’ll need new governance structures, institutions probably to help with this  transition. So I think political philosophy and political science is  going to be key to that. But I think the number one thing, first of all  is to create more abundance of resources. So that’s the number one  thing. Increase productivity, get more resources, maybe eventually get  out of the zero-sum situation. Then the second question is how to use  those resources and distribute those resources. But yeah, you can’t do  that without having that abundance first.        

## John von Neumann

Lex Fridman: 01:48:54: You mentioned to me the book, The Maniac by Benjamin Labatut, a book on first of all about you. There’s a bio about you.        

Demis Hassabis: 01:49:05: Strange, yeah.        

Lex Fridman: 01:49:06: Yes, sure. It’s unclear how much is  fiction, how much is reality. But I think the central figure that is  John von Neumann, I would say it’s a haunting and beautiful exploration  of madness and genius and let’s say the double-edged sword of discovery. And for people who don’t know, John von Neumann is a legendary mind. He contributed to quantum mechanics. He was on the Manhattan Project. He  is widely considered to be the father of or pioneer the modern computer  and AI and so on. So many people say he’s one of the smartest humans  ever, which is fascinating.        

01:49:45: And what’s also fascinating is he’s a  person who saw nuclear science and physics become the atomic bomb, so  you got to see ideas become a thing that has a huge amount of impact on  the world. He also foresaw the same thing for computing, and that’s a  little bit again, beautiful and haunting aspect of the book. Then taking a leap forward and looking at this, at least it all AlphaZero, AlphaGo  AlphaZero big moment that maybe John von Neumann’s thinking was brought  to reality. So I guess the question is what do you think if you got to  hang out with John von Neumann now, what would he say about what’s going on?        

Demis Hassabis: 01:50:35: Well, that would be an amazing  experience. He’s a fantastic mind. And I also love the way he spent a  lot of his time at Princeton at the Institute of Advanced Studies, a  very special place for thinking. And it’s amazing how much of a polymath he was and the spread of things he helped invent, including of course  the Von Neumann architecture that all the modern computers are based on. And he had amazing foresight. I think he would’ve loved where we are  today, and I think he would’ve really enjoyed AlphaGo being, he did game theory. I think he foresaw a lot of what would happen with learning  machines, systems that are grown, I think he called it rather than  programmed. I’m not sure how even maybe he wouldn’t even be that  surprised. There’s the fruition of what I think he already foresaw in  the 1950s.        

Lex Fridman: 01:51:25: I wonder what advice he would give. He got to see the building of the atomic bomb with the Manhattan Project.  I’m sure there’s interesting stuff that maybe is not talked about  enough, maybe some bureaucratic aspect, maybe the influence of  politicians, maybe not enough of picking up the phone and talking to  people that are called enemies by the said politicians. There might be  some deep wisdom that we just may have lost from that time actually.        

Demis Hassabis: 01:51:48: Yeah, I’m sure there is. I read a lot  of books for that time as well, Chronicle Time and some brilliant people involved. But I agree with you. I think maybe there needs to be more  dialogue and understanding. I hope we can learn from those times. I  think the difference here is that the AI has so many, it’s a multi-use  technology. Obviously we’re trying to do things like solve all diseases, help with energy and scarcity, these incredible things. This is why all of us and myself, I started on this journey 30 plus years ago. But of  course there are risks too. And probably Von Neumann, my guess is he  foresaw both. And I think he said, I think it’s to his wife, that  computers would be even more impactful in the world. And as we just  discussed, I think that’s right. I think it’s going to be 10 times at  least of the Industrial Revolution. So I think he’s right. So I think he would’ve been, I imagine, fascinated by where we are now.        

Lex Fridman: 01:52:53: And I think one of the, maybe you can  correct me, but one of the takeaways from the book is that reason, as  said in the book, Mad Dreams of Reason, it’s not enough for guiding  humanity as we build these super powerful technology. That there’s  something else. There’s also a religious component, whatever God,  whatever religion gives, it pulls at something in the human spirit that  raw cold reason doesn’t give us.        

Demis Hassabis: 01:53:22: And I agree with that. I think we need to approach it with whatever you want to call it, a spiritual dimension or humanist dimension. Doesn’t have to be to do with religion, but this idea of a soul, what makes us human, this spark that we have, perhaps  it’s to do with consciousness when we finally understand that, I think  that has to be at the heart of the endeavor. And technology, I’ve always seen technology as the enabler, the tools that enable us to flourish  and to understand more about the world. And I’m with Feynman on this,  and he used to always talk about science and art being companions. You  can understand it from both sides, the beauty of a flower, how beautiful it is, and also understand why the colors of the flower evolve like  that. That just makes it more beautiful, just the intrinsic beauty of  the flower.        

01:54:10: I’ve always seen it like that. And  maybe in the Renaissance times, the great discoverers then, people like  Da Vinci, I don’t think he saw any difference between science and art  and perhaps religion. Everything was, it’s just part of being human and  being inspired about the world around us. And that’s the philosophy I  tried to take. And one of my favorite philosophers is Spinoza. And I  think he combined that all very well, this idea of trying to understand  the universe and understanding our place in it. And that was his way of  understanding religion. And I think that’s quite beautiful. And for me,  all of these things are related, interrelated, the technology and what  it means to be human.        

01:54:53: And I think it’s very important though that we remember that as when we’re immersed in the technology and the  research, I think a lot of researchers that I see in our field are a  little bit too narrow and only understand the technology. And I think  also that’s why it’s important for this to be debated by society at  large. I’m very supportive of things like the AI summits that will  happen and governments understanding it. And I think that’s one good  thing about the chatbot era and the product era of AI is that everyday  person can actually feel and interact with cutting edge AI and feel it  for themselves.        

Lex Fridman: 01:55:30: Yeah, because they force the technologists to have the human conversation. Yeah, for sure.        

Demis Hassabis: 01:55:30: Yeah.        

Lex Fridman: 01:55:35: That’s the hopeful aspect of it, like  you said, it’s a dual use technology that we’re forcefully integrating  the entire humanity into it, into the discussion about AI because  ultimately AI, AGI will be used for things that states use technologies  for, which is conflict and so on. And the more we integrate humans into  this picture by having chats with them, the more we will guide.        

Demis Hassabis: 01:56:01: Yeah, be able to adapt, society will  be able to adapt to these technologies we’ve always done in the past  with the incredible technologies we’ve invented in the past.        

Lex Fridman: 01:56:10: Do you think there will be something  like a Manhattan Project where there will be an escalation of the power  of this technology and states in their old way of thinking, we’ll try to use it as weapons technologies and there will be this escalation?        

Demis Hassabis: 01:56:27: I hope not. I think that would be very dangerous to do. And I think also not the right use of the technology. I hope we’ll end up with something more collaborative if needed, more  like a CERN project where it’s research-focused and the best minds in  the world come together to carefully complete the final steps and make  sure it’s responsibly done before deploying it to the world. We’ll see.  It’s difficult with the current geopolitical climate, I think, to see  cooperation, but things can change. And I think at least on the  scientific level, it’s important for the researchers to keep in touch  and keep close to each other at least on those kinds of topics.        

Lex Fridman: 01:57:17: And I personally believe on the  education side and immigration side, it would be great if both  directions, people from the West immigrated to China and China, back.  There is some family human aspect of people just intermixing and thereby those ties grow strong. So you can’t divide against each other, this  old school way of thinking. And so multicultural, multidisciplinary  research teams working on scientific questions, that’s like the hope.  Don’t let the leaders that are warmongers divide us. I think science is  the ultimately really beautiful connector.        

Demis Hassabis: 01:57:55: Yeah, science has always been, I  think, quite a very collaborative endeavor and scientists know that it’s a collective endeavor as well, and we can all learn from each other. So perhaps it could be a vector to get a bit of cooperation.        

## p(doom)

Lex Fridman: 01:58:08: Ridiculous question, what’s your P-Doom? Probability of the human civilization destroys itself?        

Demis Hassabis: 01:58:14: Well, look, I don’t have a P-Doom  number. The reason I don’t is because I think it would imply a level of  precision that is not there. So I don’t know how people are getting  their P-Doom numbers. I think it’s a little bit of ridiculous notion  because what I would say is it’s definitely non-zero and it’s probably  non-negligible. So that in itself is pretty sobering. And my view is  it’s just hugely uncertain what these technologies are going to be able  to do, how fast are they going to take off, how controllable are they  going to be. Some things may turn out to be, and hopefully way easier  than we thought, but it may be there’s some really hard problems that  are harder than we guessed today, and I think we don’t know that for  sure. And so under those conditions of a lot of uncertainty, but huge  stakes both ways.        

01:59:09: On the one hand, we could solve all  diseases, energy problems, the scarcity problem, and then travel to the  stars and conscious of the stars and maximum human flourishing. On the  other hand, is these P-Doom scenarios. So given the uncertainty around  it and the importance of it, it’s clear to me the only rational,  sensible approach is to proceed with cautious optimism. So we want the  benefits of course, and all of the amazing things that AI can bring. And actually, I would be really worried for humanity given the other  challenges that we have, climate, aging, resources, all of that if I  didn’t know something like AI was coming down the line. How would we  solve all those other problems? I think it’s hard. So I think it could  be amazingly transformative for good. But on the other hand, there are  these risks that we know are there.        

Demis Hassabis: 02:00:00: But on the other hand, there are these risks that we know are there, but we can’t quite quantify. So the best  thing to do is to use the scientific method to do more research to try  and more precisely define those risks and of course address them. And I  think that’s what we’re doing. I think there probably needs to be 10  times more effort of that than there is now as we are getting closer and closer to the AGI line.        

Lex Fridman: 02:00:27: What would be the source of worry for  you more? Would it be human-caused or AI, AGI caused? Are humans abusing that technology versus AGI itself through mechanism that you’ve spoken  about, which is fascinating, deception or this kind of stuff getting  better and better and better secretly and then escapes?        

Demis Hassabis: 02:00:45: I think they operate over different  timescales and they’re equally important to address. So there’s just the common garden variety of bad actors using new technology, in this case, general purpose technology and repurposing it for harmful end. And  that’s a huge risk and I think that has a lot of complications because  generally I’m in huge favor of open science and open source, and in  fact, we did it with all our science projects like AlphaFold and all of  those things for the benefit of the scientific community. But how does  one restrict bad actors access to these powerful systems, whether  they’re individuals or even rogue states, but enable access at the same  time to good actors to maximally build on top of? It’s pretty tricky  problem that I’ve not heard a clear solution to. So there’s the bad  actor use case problem, and then there’s obviously, as the systems  become more agentic and closer to AGI and more autonomous, how do we  ensure the guardrails and they stick to what we want them to do and  under our control?        

Lex Fridman: 02:01:52: Yeah, I tend to, maybe my mind is  limited, worry more about the humans, so the bad actors. And there it  could be in part how do you not put destructive technology in the hands  of bad actors, but in another part from, again, geopolitical technology  perspective, how do you reduce the number of bad actors in the world?  That’s also an interesting human problem.        

Demis Hassabis: 02:02:14: Yeah, it’s a hard problem. I mean,  look, we can maybe also use the technology itself to help early warning  on some of the bad actor use cases, right? Whether that’s bio or nuclear or whatever it is, AI could be potentially helpful there as long as the AI that you’re using is itself reliable, right? So it’s a sort of  interlocking problem and that’s what makes it very tricky. And again, it may require some agreement internationally, at least between China and  the U.S. of some basic standards. Right.        

## Humanity

Lex Fridman: 02:02:50: I have to ask you about the book, The  Maniac. There’s the hand of God moment, Lee Sedol’s move 78 that perhaps the last time a human did a move of pure human genius and beat AlphaGo  or broke its brain.        

Demis Hassabis: 02:03:08: Yes.        

Lex Fridman: 02:03:08: Sorry to anthropomorphize, but it’s an interesting moment because I think in so many domains it will keep happening.        

Demis Hassabis: 02:03:14: Yeah, it’s a special moment and it was great for Lee Sedol. I think it’s in a way they were inspiring each  other. We as a team were inspired by Lee Sedol’s brilliance and  nobleness. Then maybe he got inspired by what AlphaGo was doing to then  conjure this incredible inspirational moment, captured very well in the  documentary about it. And I think that’ll continue in many domains where there’s this, at least again for the foreseeable future, of the humans  bringing in their ingenuity and asking the right question, let’s say,  and then utilizing these tools in a way that then cracks a problem.        

Lex Fridman: 02:03:58: Yeah. As the AI become smarter and  smarter, one of the interesting questions we can ask ourselves is what  makes humans special? It does feel perhaps biased that we humans are  deeply special. I don’t know if it’s our intelligence, it could be  something else, that other thing that’s outside the mad dreams of  reason.        

Demis Hassabis: 02:04:20: I think that’s what I’ve always  imagined when I was a kid and starting on this journey of I was of  course fascinated by things like consciousness, did a neuroscience PhD  to look at how the brain works, especially imagination and memory. I  focused on the hippocampus and it’s sort of going to be interesting. I  always thought the best way, of course, one can philosophize about it  and have thought experiments and maybe even do actual experiments like  you do in neuroscience on real brains. But in the end, I always imagine  that building AI, a kind of intelligent artifact, and then comparing  that to the human mind and seeing what the differences were would be the best way to uncover what’s special about the human mind, if indeed  there is anything special.        

02:05:00: And I suspect there probably is, but  it’s going to be hard to… I think this journey we’re on will help us  understand that and define that. And there may be a difference between  carbon based substrates that we are and silicon ones when they process  information. One of the best definitions I like of consciousness is it’s the way information feels when we process it, right?        

Lex Fridman: 02:05:22: Yeah.        

Demis Hassabis: 02:05:24: It could be. I mean, it’s not a very  helpful scientific explanation, but I think it’s kind of interesting  intuitive one. And so on this journey, this scientific journey we’re on  will I think help uncover that mystery.        

Lex Fridman: 02:05:36: Yeah. What I cannot create, I do not  understand. That’s somebody you deeply admire, Richard Feynman, like you mentioned. You also reach for the Wigner’s dreams of universality that  he saw in constrained domains, but also broadly generally in mathematics and so on. So many aspects on which you’re pushing towards not to start trouble at the end, but Roger Penrose.        

## Consciousness and quantum computation

Demis Hassabis: 02:06:00: Yes. Okay.        

Lex Fridman: 02:06:04: So do you think consciousness, there’s this hard problem of consciousness, how information feels. Do you think consciousness, first of all, is a computation? And if is, if it’s  information processing, like you said, everything is, is it something  that could be modeled by a classical computer?        

Demis Hassabis: 02:06:23: Yeah.        

Lex Fridman: 02:06:24: Or is it a quantum mechanical in nature?        

Demis Hassabis: 02:06:26: Well, look, Penrose is an amazing  thinker, one of the greatest of the modern era, and we’ve had a lot of  discussions about this. Of course, we cordially disagree, which is I  feel like… I mean, he collaborated with a lot of good neuroscientists to see if he could find mechanisms for quantum mechanics behavior in the  brain. And to my knowledge, they haven’t found anything convincing yet.  So my betting is that it’s mostly it is just classical computing that’s  going on in the brain, which suggests that all the phenomena are  modelable or mimicable by a classical computer. But we’ll see. There may be this final mysterious things of the feeling of consciousness, the  qualia, these kinds of things that philosophers debate where it’s unique to the substrate.        

02:07:12: We may even come towards understanding that when if we do things like neural link or have neural interfaces to the AI systems, which I think we probably will eventually, maybe to  keep up with the AI systems, we might actually be able to feel for  ourselves what it’s like to compute on silicon, right? And maybe that  will tell us. So I think it’s going to be interesting. I had a debate  once with the late Daniel Dennett about why do we think each other are  conscious? Okay, so it’s for two reasons. One is you’re exhibiting the  same behavior that I am. So that’s one thing. Behaviorally you seem like a conscious being if I am.        

02:07:49: But the second thing which is often  overlooked is that we’re running on the same substrate. So if you’re  behaving in the same way and we’re running on the same substrate, it’s  most parsimonious to assume you’re feeling the same experience that I’m  feeling. But with an AI that’s on silicon, we won’t be able to rely on  the second part, even if it exhibits the first part, that behavior looks like a behavior of a conscious being. It might even claim it is, but we wouldn’t know how it actually felt and it probably couldn’t know what  we felt, at least in the first stages. Maybe when we get to  superintelligence and the technologies that builds, perhaps we’ll be  able to bridge that.        

Lex Fridman: 02:08:26: No, I mean that’s a huge test for radical empathy is to empathize with a different substrate.        

Demis Hassabis: 02:08:32: Right. Exactly. We’ve never had to confront that before.        

Lex Fridman: 02:08:36: Yeah. So maybe through brain computer interfaces be able to truly empathize what it feels like to be a computer, to compute.        

Demis Hassabis: 02:08:42: Well, for information to be computed not on a carbon system.        

Lex Fridman: 02:08:46: I mean, that’s deeply… Some people kind of think about that with plants, with other life forms which are different.        

Demis Hassabis: 02:08:51: Yes, it could be exactly.        

Lex Fridman: 02:08:53: Similar substrate, but sufficiently  far enough on the evolutionary tree that it requires a radical empathy,  but to do that with a computer.        

Demis Hassabis: 02:09:02: I mean, look, there are animal studies on this. Of course, higher animals like killer whales and dolphins and  dogs and monkeys, they have some, and elephants, they have some aspects  certainly of consciousness, right? Even though they might not be that  smart on an IQ sense. So we can already empathize with that and maybe  even some of our systems one day, like we built this thing called  DolphinGemma, which a version of our system was trained on dolphin and  whale sounds, and maybe we’ll be able to build an interpreter or  translator at some point which would be pretty cool.        

Lex Fridman: 02:09:35: What gives you hope for the future of human civilization?        

Demis Hassabis: 02:09:38: Well, what gives me hope is that I  think our almost limitless ingenuity, first of all. I think the best of  us and the best human minds are incredible. And I love meeting and  watching any human that’s the top of their game, whether that’s sport or science or art, it’s just nothing more wonderful than that, seeing them in their element in flow. I think it’s almost limitless. Our brains are general systems, intelligent systems, so I think it’s almost limitless  what we can potentially do with them. And then the other thing is our  extreme adaptability. I think it’s going to be okay in terms of there’s  going to be a lot of change, but look where we are now without  effectively our hunter-gatherer brains.        

02:10:24: How is it we can cope with the modern  world, right? Flying on planes, doing podcasts, playing computer games  and virtual simulations. I mean, it’s already mind blowing given that  our mind was developed for hunting buffaloes on the tundra. And so I  think this is just the next step, and it’s actually kind of interesting  to see how society’s already adapted to this mind blowing AI technology  we have today already. It’s sort of like, “Oh, I talked to chat bots.  Totally fine.”        

Lex Fridman: 02:10:54: And it’s very possible that this very  podcast activity, which I’m here for, will be completely replaced by AI. I’m very replaceable and I’m waiting for it.        

Demis Hassabis: 02:11:02: Not to the level that you can do it, Lex, I don’t think.        

Lex Fridman: 02:11:04: Thank you. That’s what we humans do to each other. We compliment.        

Demis Hassabis: 02:11:08: Exactly.        

Lex Fridman: 02:11:09: All right. And I’m deeply grateful for us humans to have this infinite capacity for curiosity, adaptability,  like you said, and also compassion and ability to love.        

Demis Hassabis: 02:11:18: Exactly.        

Lex Fridman: 02:11:19: All of those human things.        

Demis Hassabis: 02:11:19: All the things that are deeply human.        

Lex Fridman: 02:11:21: Well, this is a huge honor, Demis. You are one of the truly special humans in the world. Thank you so much for doing what you do and for talking today.        

Demis Hassabis: 02:11:29: Well, thank you very much, Lex.        

Lex Fridman: 02:11:32: Thanks for listening to this  conversation with Demis Hassabis. To support this podcast, please check  out our sponsors in the description and consider subscribing to this  channel. And now let me answer some questions and try to articulate some things I’ve been thinking about. If you’d like to submit questions  including in audio and video form, go to lexfridman.com/ama. I got a lot of amazing questions, thoughts and requests from folks. I’ll keep  trying to pick some randomly and comment on it at the end of every  episode. I got a note on May 21st this year that said, “Hi, Lex. 20  years ago today, David Foster Wallace delivered his famous This is Water speech at Kenyon College. What do you think of this speech?        

## David Foster Wallace

02:12:21: Well, first, I think this is probably  one of the greatest and most unique commencement speeches ever given,  but of course, I have many favorites, including the one by Steve Jobs.  And David Foster Wallace is one of my favorite writers and one of my  favorite humans. There’s a tragic honesty to his work, and it always  felt as if he was engaging in a constant battle with his own mind, and  the writing, his writing were kind of his notes from the front lines of  that battle. Now onto the speech, let me quote some parts. There’s of  course the parable of the fish and the water that goes, there are these  two young fish swimming along and they happen to meet an older fish  swimming the other way who nods at them and says, “Morning boys, how’s  the water?” And the two young fish swim on for a bit and then eventually one of them looks over at the other and goes, “What the hell is water?” In the speech, David Foster Wallace goes on to say, “The point of the  fish story is merely that the most obvious important realities are often the ones that are hardest to see and talk about. Stated as an English  sentence of course, this is just the banal platitude, but the fact is  that in the day to day trenches of adult existence, banal platitudes can have a life or death importance, or so I wish to suggest to you in this dry and lovely morning.” I have several takeaways from this parable and the speech that follows. First, I think we must question everything,  and in particular, the most basic assumptions about our reality, our  life, and the very nature of existence, and that this project is a  deeply personal one. In some fundamental sense, nobody can really help  you in this process of discovery.        

02:14:23: The call to action here, I think, from David Foster Wallace as he puts it, is to ” To be just a little less  arrogant, to have just a little more critical awareness about myself and my certainties because a huge percentage of the stuff that I tend to be automatically certain of is it turns out totally wrong and deluded.”  All right, back to me. Lex speaking. Second takeaway is that the central spiritual battles of our life are not fought on a mountain top  somewhere at a meditation retreat, but it’s fought in the mundane  moments of daily life.        

02:15:08: Third takeaway is that we too easily  give away our time and attention to the multitude of distractions that  the world feeds us, the insatiable black holes of attention. David  Foster Wallace’s call to action in this case is to be deeply aware of  the beauty in each moment and to find meaning in the mundane. I often  quote David Foster Wallace in his advice that the key to life is to be  unborable, and I think this is exactly right. Every moment, every  object, every experience when looked at closely enough contains within  it infinite richness to explore. And since Demis Hassabis of this very  podcast episode and I are such fans of Richard Feynman, allow me to also quote Mr. Feynman on this topic as well.        

02:16:04: “I have a friend who’s an artist and  has sometimes taken a view which I don’t agree with very well. He’ll  hold up a flower and say, “Look how beautiful it is,” and I’ll agree.  Then he says, “I as an artist can see how beautiful this is, but you as a scientist take this all apart and it becomes a dull thing,” and I think that’s kind of nutty. First of all, the beauty that he sees is  available to other people and to me too, I believe. Although I may not  be quite as refined aesthetically as he is, I can appreciate the beauty  of a flower. At the same time, I see much more about the flower than he  sees. I could imagine the cells in there, the complicated actions inside which also have beauty. I mean, it’s not just beauty at this dimension  at one centimeter, there’s also beauty at the smaller dimensions.”        

02:17:05: “Their inner structure, also the  processes, the fact that the colors and the flower evolved in order to  attract the insects to pollinate it is interesting. It means that the  insects can see the color. It adds a question. Does this aesthetic sense also exist in lower forms? Why is it aesthetic? All kinds of  interesting questions, which the science knowledge only adds to the  excitement, the mystery, and the awe of a flower. It only adds.”        

02:17:36: All right, back to David Foster  Wallace’s speech. He has a great story in there that I particularly  enjoy. It goes, there are these two guys sitting together in a bar in  the remote Alaskan wilderness. One of the guys is religious, the other  is an atheist, and the two are arguing about the existence of God with  that special intensity that comes after about the fourth beer. And the  atheist says, “Look, it’s not like I don’t have actual reasons for not  believing in God. It’s not like I haven’t ever experimented with the  whole God and prayer thing. Just last month, I got caught away from the  camp in that terrible blizzard, and I was totally lost and I couldn’t  see a thing and it was 50 below. So I tried it. I fell in my knees in  the snow and cried out, ‘Oh God, if there is a God, I’m lost in this  blizzard and I’m going to die if you don’t help me.”        

02:18:35: And now back in the bar, the religious guy looks at the atheist all puzzled, “Well, then you must believe  now?” he says, “After all, there you are, alive.” The atheist just rolls his eyes. “No, man. All that happened was a couple of Eskimos happened  to be wandering by and showed me the way back to the camp.” All this, I  think, teaches us that everything is a matter of perspective and that  wisdom may arrive if we have the humility to keep shifting and expanding our perspective on the world. Thank you for allowing me to talk a bit  about David Foster Wallace. He’s one of my favorite writers and he’s a  beautiful soul.        

## Education and research

02:19:20: If I may, one more thing I wanted to  briefly comment on. I find myself to be in this strange position of  getting attacked online often from all sides, including being lied about sometimes through selective misrepresentation, but often through  downright lies. I don’t know how else to put it. This all breaks my  heart, frankly, but I’ve come to understand that it’s the way of the  internet and the cost of the path I’ve chosen. There’s been days when  it’s been rough on me mentally. It’s not fun being lied about,  especially when it’s about things that are usually for a long time have  been a source of happiness and joy for me. But again, that’s life.        

02:20:04: I’ll continue exploring the world of  people and ideas with empathy and rigor, wearing my heart on my sleeve  as much as I can. For me, that’s the only way to live. Anyway, a common  attack on me is about my time at MIT and Drexel, two great universities I love and have tremendous respect for. Since a bunch of lies have  accumulated online about me on these topics, to a sad and at times  hilarious degree, I thought I would once more state the obvious facts  about my bio for the small number of you who may care. TLDR, two things. First, as I say often, including in a recent podcast episode that  somehow was listened to by many millions of people, I proudly went to  Drexel University for my bachelor’s, master’s, and doctorate degrees.        

02:20:59: Second, I am a research scientist at  MIT and have been there in a paid research position for the last 10  years. Allow me to elaborate a bit more on these two things now, but  please skip if this is not at all interesting. So like I said, a common  attack on me is that I have no real affiliation with MIT. The  accusation, I guess, is that I’m falsely claiming an MIT affiliation  because I taught a lecture there once. Nope, that accusation against me  is a complete lie. I have been at MIT for over 10 years in a paid  research position from 2015 to today. To be extra clear, I’m a research  scientist at MIT working in LIDS, the Laboratory for Information and  Decision Systems in the College of Computing. For now, since I’m still  at MIT, you can see me in the directory and on the various lab pages.        

02:22:05: I have indeed given many lectures at  MIT over the years, a small fraction of which I posted online. Teaching  for me always has been just for fun and not part of my research work. I  personally think I suck at it, but I have always learned and grown from  the experience. It’s like Feynman spoke about, if you want to understand something deeply, it’s good to try to teach it. But like I said, my  main focus has always been on research. I published many peer-reviewed  papers that you can see in my Google Scholar profile. For my first four  years at MIT, I worked extremely intensively. Most weeks were 80 to  100-hour work weeks. After that, in 2019, I still kept my research  scientist position, but I split my time taking a leap to pursue projects in AI and robotics outside MIT and to dedicate a lot of focus to the  podcast.        

02:23:03: As I’ve said, I’ve been continuously  surprised just how many hours preparing for an episode takes. There are  many episodes of the podcast for which I have to read, write, and think  for 100, 200 or more hours across multiple weeks and months. Since 2020, I have not actively published research papers. Just like the podcast, I think it’s something that’s a serious full-time effort. But not  publishing and doing full-time research has been eating at me because I  love research and I love programming and building systems that test out  interesting technical ideas, especially in the context of human-AI or  human-robot interaction. I hope to change this in the coming months and  years.        

02:23:52: What I’ve come to realize about myself is if I don’t publish or if I don’t launch systems that people use, I  definitely feel like a piece of me is missing. It legitimately is a  source of happiness for me. Anyway, I’m proud of my time at MIT. I was  and am constantly surrounded by people much smarter than me, many of  whom have become lifelong colleagues and friends. MIT is a place I go to escape the world, to focus on exploring fascinating questions at the  cutting edge of science and engineering. This, again, makes me truly  happy and it does hit pretty hard on a psychological level when I’m  getting attacked over this. Perhaps I’m doing something wrong. If I am, I will try to do better.        

02:24:43: In all this discussion of academic  work, I hope you know that I don’t ever mean to say that I’m an expert  at anything. In the podcast and in my private life, I don’t claim to be  smart. In fact, I often call myself an idiot and mean it. I try to make  fun of myself as much as possible, and in general to celebrate others  instead. Now to talk about Drexel University, which I also love, am  proud of and am deeply grateful for my time there. As I said, I went to  Drexel for my bachelor’s, master’s, and doctorate degrees in computer  science and electrical engineering. I’ve talked about Drexel many times, including, as I mentioned, at the end of a recent podcast, the Donald  Trump episode. funny enough, that was listened to by many millions of  people where I answered a question about graduate school and explained  my own journey at Drexel and how grateful I am for it.        

02:25:46: If it’s at all interesting to you,  please go listen to the end of that episode or watch the related clip.  At Drexel, I met and worked with many brilliant researchers and mentors  from whom I’ve learned a lot about engineering, science and life. There  are many valuable things I gained from my time at Drexel. First, I took a large number of very difficult math and theoretical computer science  courses. They taught me how to think deeply and rigorously, and also how to work hard and not give up even if it feels like I’m too dumb to find a solution to a technical problem.        

02:26:21: Second, I programmed a lot during that time, mostly C, C++. I programmed robots, optimization algorithms,  computer vision systems, wireless network protocols, multimodal machine  learning systems, and all kinds of simulations of physical systems. This is where I really developed a love for programming, including, yes,  Emacs And the Kinesis keyboard. I also, during that time, read a lot, I  played a lot of guitar, wrote a lot of crappy poetry, and trained a lot  in judo and jiu-jitsu, which I cannot sing enough praises to. Jiu-jitsu  humbled me on a daily basis throughout my twenties, and it still does to this very day whenever I get a chance to train.        

02:27:13: Anyway, I hope that the folks who  occasionally get swept up in the chanting online crowds that want to  tear down others don’t lose themselves in it too much. In the end, I  still think there’s more good than bad in people. But we’re all each of  us a mixed bag. I know I am very much flawed. I speak awkwardly. I  sometimes say stupid shit. I can get irrationally emotional. I can be  too much of a dick when I should be kind. I can lose myself in a biased  rabbit hole before I wake up to the bigger, more accurate picture of  reality. I’m human and so are you for better or for worse, and I do  still believe we’re in this whole beautiful mess together. I love you  all.        