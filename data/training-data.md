## Training Data (Lynk & Co Owner Manual)

The training corpus is sourced from the **Lynk & Co vehicle owner manual** (PDF). It covers in-cabin components, operations, safety, maintenance, and user guidance. Below is an example page illustrating how the manual content is structured and annotated (useful for PDF chunking + retrieval).

### Example: Cargo / Storage — Front Cabin Storage

**Front-row storage locations**
- Sun visor card/ticket holder
- Glove box
- Door storage compartments
- Glasses compartment
- Front auxiliary instrument panel (center console area)
- Front center armrest storage compartment
- Side net pocket on the front auxiliary instrument panel

**Auxiliary instrument panel (center console) labeled parts**
- `01` Wireless charging pad / storage tray
- `02` Cup holder
- `03` Storage compartment

**Note**
- Do not forcibly insert containers that do not fit into the cup holder.

---

## Test Set Questions (JSON Schema Example)

The test set is organized as JSON objects with a `question` field and multiple answer slots (`answer_1`, `answer_2`, `answer_3`) for candidate outputs or evaluation variants.

```json
{
  "question": "How do the auxiliary instrument panel buttons operate the center display?",
  "answer_1": "",
  "answer_2": "",
  "answer_3": ""
},
{
  "question": "How do you wake the center display from the locked state?",
  "answer_1": "",
  "answer_2": "",
  "answer_3": ""
},
{
  "question": "How do you correctly use the cervical spine (neck) protection system?",
  "answer_1": "",
  "answer_2": "",
  "answer_3": ""
}
