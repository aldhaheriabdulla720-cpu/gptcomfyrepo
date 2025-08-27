# ComfyUI + WanVideoWrapper (Serverless Image)

Prebuilt image for RunPod Serverless using the official ComfyUI worker base,
with WanVideoWrapper + VideoHelperSuite baked in.

## How to use
1. Create a new GitHub repo and upload these files.
2. Add repo secrets in GitHub:
   - `DOCKERHUB_USERNAME`
   - `DOCKERHUB_TOKEN` (Docker Hub access token)
3. Push to `main` → GitHub Actions builds and pushes your image to Docker Hub.
4. In RunPod:
   - **Container Image**: `docker.io/<your-username>/comfyui-wanvideo:main` (or a tag from Actions)
   - **Start Command**: *(leave empty)*
   - **Env vars**:
     - `COMFYUI_MODEL_PATH=/workspace/ComfyUI/models`
     - `COMFYUI_CUSTOM_NODE_PATH=/workspace/ComfyUI/custom_nodes`
   - **Scaling**: Max Workers=1, Auto Scaling=Request Count, Req=999, Idle Timeout 30–60s, Execution Timeout 600s.
   - (Optional) Mount a **Network Volume** at `/workspace/ComfyUI/models` for checkpoints.

## Smoke tests
Use the RunPod **Requests** tab:
- Image:
```json
{ "input": { "workflow": { "last_node_id": 2, "nodes": [
  { "id": 1, "type": "TextPrompt", "inputs": { "text": "a single red balloon" } },
  { "id": 2, "type": "SaveImage", "inputs": { "filename_prefix": "smoke_test", "save_path": "/workspace/ComfyUI/output/" } }
]}}}
```
- Video:
```json
{ "input": { "workflow": { "last_node_id": 3, "nodes": [
  { "id": 1, "type": "TextPrompt", "inputs": { "text": "A cat playing piano" } },
  { "id": 2, "type": "WanVideoWrapper", "inputs": { "prompt": "A cat playing piano", "num_frames": 16, "fps": 8, "resolution": "512x512" } },
  { "id": 3, "type": "VideoHelperSuite.SaveVideo", "inputs": { "filename_prefix": "test_output", "frame_rate": 8, "save_path": "/workspace/ComfyUI/output/" } }
]}}}
```
