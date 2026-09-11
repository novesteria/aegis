# Aegis Validator — Layer Index

This is the canonical list of every layer Aegis runs, in pipeline
execution order. The registry source is
[`aegis/checks/__init__.py`](../aegis/checks/__init__.py).

## Summary

| Kind | Count |
|---|---|
| Deterministic | 19 |
| Hybrid (LLM + deterministic override) | 2 |
| **Total** | **21** |

## Numbering

Before the checks run, the pipeline performs three preparatory stages
(stack detection, code-path materialisation, environment setup). They
are not check layers and are not counted. The 21 check layers are
numbered #1 to #21 below.

## Layers

| # | Name | Kind | Applies to | Source |
|---|---|---|---|---|
| 1 | `python_imports` | deterministic | python | [`aegis/checks/python_imports.py`](../aegis/checks/python_imports.py) |
| 2 | `python_completeness` | deterministic | python | [`aegis/checks/python_completeness.py`](../aegis/checks/python_completeness.py) |
| 3 | `python_deps_completeness` | deterministic | python | [`aegis/checks/python_deps_completeness.py`](../aegis/checks/python_deps_completeness.py) |
| 4 | `router_prefix_consistency` | deterministic | python | [`aegis/checks/router_prefix_consistency.py`](../aegis/checks/router_prefix_consistency.py) |
| 5 | `node_deps_completeness` | deterministic | node | [`aegis/checks/node_deps_completeness.py`](../aegis/checks/node_deps_completeness.py) |
| 6 | `css_completeness` | deterministic | node, static_html | [`aegis/checks/css_completeness.py`](../aegis/checks/css_completeness.py) |
| 7 | `react_prop_consistency` | deterministic | node | [`aegis/checks/react_prop_consistency.py`](../aegis/checks/react_prop_consistency.py) |
| 8 | `named_import_consistency` | deterministic | node | [`aegis/checks/named_import_consistency.py`](../aegis/checks/named_import_consistency.py) |
| 9 | `import_case_consistency` | deterministic | node, python | [`aegis/checks/import_case_consistency.py`](../aegis/checks/import_case_consistency.py) |
| 10 | `duplicate_type_declarations` | deterministic | node | [`aegis/checks/duplicate_type_declarations.py`](../aegis/checks/duplicate_type_declarations.py) |
| 11 | `hook_destructure_consistency` | deterministic | node | [`aegis/checks/hook_destructure_consistency.py`](../aegis/checks/hook_destructure_consistency.py) |
| 12 | `ast_brace_balance` | deterministic | node, static_html | [`aegis/checks/brace_balance.py`](../aegis/checks/brace_balance.py) |
| 13 | `static_imports` | deterministic | node, static_html | [`aegis/checks/static_imports.py`](../aegis/checks/static_imports.py) |
| 14 | `html_js_id_parity` | deterministic | static_html, node | [`aegis/checks/html_js_id_parity.py`](../aegis/checks/html_js_id_parity.py) |
| 15 | `interactivity` | deterministic | static_html, node | [`aegis/checks/interactivity.py`](../aegis/checks/interactivity.py) |
| 16 | `js_syntax` | deterministic | static_html, node | [`aegis/checks/js_syntax.py`](../aegis/checks/js_syntax.py) |
| 17 | `npm_install` | deterministic | node | [`aegis/checks/npm_install.py`](../aegis/checks/npm_install.py) |
| 18 | `tsc` | deterministic | node | [`aegis/checks/tsc.py`](../aegis/checks/tsc.py) |
| 19 | `pytest` | deterministic | python | [`aegis/checks/pytest_check.py`](../aegis/checks/pytest_check.py) |
| 20 | `design_fidelity` | hybrid | static_html, node, python | [`aegis/checks/design_fidelity.py`](../aegis/checks/design_fidelity.py) |
| 21 | `feature_coverage` | hybrid | static_html, node, python | [`aegis/checks/feature_coverage.py`](../aegis/checks/feature_coverage.py) |

## Pluggable LLM client

Layers #20 and #21 invoke an `LLMClient` (see
[`aegis/llm_client.py`](../aegis/llm_client.py)). Aegis ships an
`AnthropicClient` against the Anthropic SDK; the `LLMClient` Protocol
allows alternative backends. When no client is configured (or
`--no-llm` is passed), these layers skip cleanly.

## Layer ordering

The pipeline runs layers in the order declared in
`aegis/checks/__init__.py:LAYERS`. Structural layers (AST, balance)
run first; subprocess layers (`npm_install`, `tsc`, `pytest`) follow;
hybrid LLM layers run last. A failure does not short-circuit the
pipeline — every applicable layer reports its own verdict and the
final pass/fail is the conjunction.
