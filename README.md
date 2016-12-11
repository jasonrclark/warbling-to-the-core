Over on the Shoes 4 project we've been having some issues with our packaged apps. (A PR which is fiddling around with some of this is https://github.com/shoes/shoes4/pull/1324, although I may be closing that and starting some smaller pieces).

This repo demonstrates the problem in minimal form. It's a single Ruby file that depends on `shoes-core`. When we build and run the jar (checked in here for ease of running), you get the following:

```
✔ ♥♥♥♥ [jruby-9.1.6.0] (master)*
~/source/warbling-to-the-core:java -jar warbling-to-the-core.jar
Ignoring shoes-core-4.0.0.pre7 because its extensions are not built.  Try: gem pristine shoes-core --version 4.0.0.pre7
LoadError: no such file to load -- shoes/version
  require at org/jruby/RubyKernel.java:959
  require at uri:classloader:/META-INF/jruby.home/lib/ruby/stdlib/rubygems/core_ext/kernel_require.rb:55
   <main> at uri:classloader:/warbling-to-the-core/run.rb:1
     load at org/jruby/RubyKernel.java:977
   <main> at uri:classloader:/META-INF/main.rb:1
  require at org/jruby/RubyKernel.java:959
   (root) at uri:classloader:/META-INF/main.rb:1
   <main> at uri:classloader:/META-INF/jruby.home/lib/ruby/stdlib/rubygems/core_ext/kernel_require.rb:1
ERROR: org.jruby.embed.EvalFailedException: (LoadError) no such file to load -- shoes/version
```

Did a little backtracking, and it appears to be coming from https://github.com/rubygems/rubygems/blob/78bc3b8a6816a74a82e617a0fd65777816d844af/lib/rubygems/basic_specification.rb#L71-L77. `shoes-core` does use a "native extension", but it's just for a funny installation dance we have to do... Not sure how to coerce warbler/rubygems to load us anyway here.
