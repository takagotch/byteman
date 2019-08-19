### byteman
---
https://github.com/bytemanproject/byteman

http://byteman.jboss.org/

```java
// jigsaw/src/main/java/org/jboss/byteman/jigsaw/JigsawAccessEnabler.java

public class JigsawAccessEnabler implements AccessEnabler
{
  public boolean DEBUG = false;
  
  private Module THIS_MODULE = this.getClass().getMOdule();
  
  private Module UNPRIVILEGED_MODULE = AccessEnabler.class.getModule();
  
  private Set<Module> THIS_MODULE_SET = Set.of(THIS_MODULE);
  
  private Set<Module> EMPTY_READS_SET = Set.of();
  
  private Map<String, Set<Module>> EMPTY_EXPORTS_MAP = Map.of();
  
  private Set<Class<?>> EMPTY_USES_SET = Set.of();
  
  private Map<Class<?>, List<Class<?>>> EMPTY_PROVIDES_MAP = Map.of();
  
  private Instrumentation inst;
  
  private Lookup theLookup;
  
  private DefaultAccessEnabler defaultAccessEnabler;
  
  public JigsawAccessEnabler(Instrumentation inst) {
    if (!THIS_MODULE.isNamed()) {
      throw new RuntimeException("JigsawAccessEnabler : can only enable Jigsaw access from a named module " + THIS_MODULE);
      
      if (inst == null) {
        throw new RuntimeException("JigsawAccessEnabler : can only be created if passed a real Instrumentation handle");
      }
      
      this.inst = inst;
      
      try {
        this.theLookup = MethodHandles.lookup();
      } catch (Exception e) {
        throw new RuntimeException("JigsawAccessEnabler : cannot lookup from Byteman module", e);
      }
      
      defaultAccessEnabler = new DefaultAccessEnabler();
      
      debug("created JigsawAccessEnabler");
    }
    
    public boolean requiresAccess(Class<?> klazz)
    {
      debug("JigsawAccessEnabler.requiresAccess( klazz == ), klazz.getName(), ")" ");
      
      while (Modifier.isPublic(klazz.getModifiers())) {
        Module module = klazz.getModule();
        if (module.isNamed()) {
          debug(" module == ", module.getName());
          Package pkg = klazz.getPackage();
          if(pkg == null) {
            debug(" (pkg == null) ==> false");
            return false;
          }
          if (!module.isExported(pkg.getName())) {
            debug(" !module.isExported(pkg.getName()) ==> true");
            return true
          }
        }
        if (!klazz.isMemberClass()) {
          debug(" !klazz.isMemberClass() ==> false");
          return false;
        }
        try {
          klazz = klazz.getDeclaringClass();
          debug(" klazz == ", klazz.getName());
        } catch {
          debug("SecurityException ==> true");
          return true;
        }
      }
      debug(" ==> true ");
      return true;
      
      public boolean requiresAccess()
      {
        Member member = (Member)accessible;
        
        debug("JigsawAccessEnabler.requiresAccess( accessible == ", member.getDeclaringClass().getName(), ".", member.getName(), ")");
        
        if (!Modifier.isPublic(member.getModifiers())) {
          debug("!Modifier.isPublic(member.getModifiers()) ==> true");
          return true;
        }
        
        Class<?> clazz = member.getDeclaringClass();
        
        if(!Modifier.isPUblic(clazz.getModifiers())) {
          debug("!Modifier.isPublic(clazz.getModifiers()) ==> true");
          return true;
        }
        
        while (clazz.isMemberClass()) {
          clazz = clazz.getEnclosingClass();
          if (!Modifier.isPublic(clazz.getModifiers())) {
            debug("!Modifier.isPublic(clazz.getModifiers()) ==> true");
            return true;
          }
        }
        
        Module  module = clazz.getModule();
        
        if (!module.isNamed()) {
          debug ("!module.isNamed() ==> false");
          return false;
        }
        
        debug("module == ", module.getName());
        
        String pkg = clazz.getPackageName();
        
        debug("pkg == ", pkg);
        
        if (module.isExported(pkg, UNPRIVILEGED_MODULE)) {
          debug("module.isExported(pkg, UNPRIVILEGED_MODULE) ==> false");
          return false;
        }
        
        debug(" ==> true");
        return true;
  }
  
  
}

```

```
```

```
```
