| Pattern | Source file in Browser-Use | Lands in Sensei file | Owner |
|---|---|---|---|
| JSON output contract | `system_prompt.md` §"Output Format" | `master_ai/contracts/output_contract.py` (new) | Claude |
| Element indexing | `controller.py` + `dom/views.py` | `master_ai/page_context_serializer.py` + `service_worker.js` ref_map | Claude (server) + eyes-side (client follow-up) |
| Screenshot ground-truth | `agent/service.py` state-update loop | `master_ai/loop.py` + `side_panel.js` promote `runQuickModeLoop` | Claude (server) + eyes-side (client follow-up) |
| Action chaining | `controller.py` multi-act | `master_ai/dispatcher.py` | Claude |
| Loop detection | `agent/service.py` history-hash check | `master_ai/loop_fsm.py` | Claude |
