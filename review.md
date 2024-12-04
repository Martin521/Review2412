#  PR [#18049](https://github.com/dotnet/fsharp/pull/18049) "Scoped Nowarn"

## History

- A very old feature request ([#278](https://github.com/fsharp/fslang-suggestions/issues/278))

- "Approved in principle", and a first [outline](https://github.com/fsharp/fslang-suggestions/issues/278#issuecomment-429604143) of an approach

- More requests and [use cases](https://github.com/fsharp/fslang-suggestions/issues/278#issuecomment-2265500124) for the feature, and converging discussion

- A  [RFC PR](https://github.com/fsharp/fslang-design/pull/782) and [discussion](https://github.com/fsharp/fslang-design/discussions/786)
  - Discussion and convergence on the interaction with `#line` directives
  - Little feedback otherwise
  - I still suspect there might be more contentious items in the proposed [RFC](https://github.com/fsharp/fslang-design/blob/72ac047ee990e387caf1a0d76024c49babe9d1e8/drafts/FS-1146-scoped-nowarn.md)
    - multiline, scripts, diagnostics, surface area / AST changes

- PR [#18049](https://github.com/dotnet/fsharp/pull/18049)

## Background

- [Compilation flow](https://github.com/Martin521/Review2412/blob/main/CompilerFlowChart.md)
  - [lex.fsl](https://github.com/dotnet/fsharp/blob/935b796dc841b6346f655421bb791c1764ab1570/src/Compiler/lex.fsl#L1057), [lex.fs](https://github.com/Martin521/Review2412/blob/12ca289ded0c9fcd490633f168e0343bcf5255a0/fs/lex.fs#L2925), [pars.fsy](https://github.com/dotnet/fsharp/blob/935b796dc841b6346f655421bb791c1764ab1570/src/Compiler/pars.fsy#L480), [pars.fs](https://github.com/Martin521/Review2412/blob/12ca289ded0c9fcd490633f168e0343bcf5255a0/fs/pars.fs#L3172)

- complications
  - no preprocessor => late pragma processing
  - streaming => ruminating error logging (regurgitating diagnosticsLogger)
  - #line => see pars.fs/fsy, [range.fs](https://github.com/dotnet/fsharp/blob/935b796dc841b6346f655421bb791c1764ab1570/src/Compiler/Utilities/range.fs#L266)

- scoped #nowarn (i.e. anywhere) can no longer be processed in the parser (i.e. [integrated](https://github.com/fsharp/fslang-spec/blob/main/releases/FSharp-Spec-4.1.2024-10-02.md#10-namespaces-and-modules) in the language grammar)
  - move to tokenizer (lex.fsl)
  - store the warn scopes in [DiagnosticsOptions](https://github.com/dotnet/fsharp/blob/main/src/Compiler/Facilities/DiagnosticOptions.fs)

## PR Review

- [WarnScopes.fsi](https://github.com/dotnet/fsharp/blob/7498b0ba6dd99f6142b4cf3224c1766336abfdb3/src/Compiler/SyntaxTree/WarnScopes.fsi)
  - ==> fix xml comment in line 17
- [1: Feature flag, baseline tests and WarnScopes module](https://github.com/dotnet/fsharp/pull/18049/commits/7498b0ba6dd99f6142b4cf3224c1766336abfdb3)
  - ==> remove .fantomasignore "nullness" section
  - [WarnScopes.fs](https://github.com/dotnet/fsharp/blob/7498b0ba6dd99f6142b4cf3224c1766336abfdb3/src/Compiler/SyntaxTree/WarnScopes.fs)
  - ==> rename TempData to LexbufData
- [2: changes to lexing and parsing](https://github.com/dotnet/fsharp/pull/18049/commits/95908998d9c745ff112093b06b2a6e79c7a51213)
  
- [3: remove legacy #nowarn processing](https://github.com/dotnet/fsharp/pull/18049/commits/7b2eeb2b9085c924b27be0005a8c0ae8cefe67e7)
  - ==> fsi.fs:3695: use tcConfigB.diagnosticsOptions, no need to thread diagnosticsOptions through the function call
- [4: integrate the new functionality and add tests](https://github.com/dotnet/fsharp/pull/18049/commits/4520e55472c4b8a1fc09b859ee1b3ed46e06c30c)
- [5: add warn directive trivia](https://github.com/dotnet/fsharp/pull/18049/commits/0fdaa43069baea18e42c43ffb45d5179c92bf527)
  - start with SyntaxTrivia.fs
- [6: enable warn directive trivia](https://github.com/dotnet/fsharp/pull/18049/commits/fb848d9deb957661eff94101ae1f2c9cbfde4e29)
- [7: remove defunct types and parameters](https://github.com/dotnet/fsharp/pull/18049/commits/e623fde60e500cd472efdbac0ac7dec7abfd4808)
- [ilverify](https://github.com/dotnet/fsharp/pull/18049/commits/62eb32a2d1495cc0967312bd475f050d6a197479)

  
