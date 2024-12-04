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
  - [lex.fsl](https://github.com/dotnet/fsharp/blob/935b796dc841b6346f655421bb791c1764ab1570/src/Compiler/lex.fsl#L1057)

- complications
  - no preprocessor => late pragma processing
  - streaming => ruminating error logging (regurgitating diagnosticsLogger)
  - #line => see pars.fs/fsy, range.fs

- #nowarn in parser not longer possible

## PR Review

- show WarnScopes.fsi
- walk through commit 1 diff
- (if time) walk through WarnScopes.fs ==> rename TempData to LexbufData
- walk through the other commits
  - 3: ==> fsi.fs:3695: use tcConfigB.diagnosticsOptions, no need to thread diagnosticsOptions through the function call
  - 5: (start with SyntaxTrivia.fs)
  
