<script setup lang="ts">
import { XmlParser, Xslt } from 'xslt-processor';
import { useValidation } from '@/composable/validation';

const xslt = ref('');
const xmlInput = ref('');
const xmlOutput = computedAsync(async () => {
  const xmlString = xmlInput.value;
  const xsltString = xslt.value;

  const xsltProcessor = new Xslt();
  const xmlParser = new XmlParser();

  try {
    return await xsltProcessor.xsltProcess(
      xmlParser.xmlParse(xmlString),
      xmlParser.xmlParse(xsltString),
    );
  }
  catch (e: any) {
    return e.toString();
  }
});

const xsltValidation = useValidation({
  source: xslt,
  rules: [
    {
      validator: (v) => {
        const xmlParser = new XmlParser();
        xmlParser.xmlParse(v);
      },
      message: 'Provided XSLT is not valid.',
    },
  ],
});
const xmlInputValidation = useValidation({
  source: xmlInput,
  rules: [
    {
      validator: (v) => {
        const xmlParser = new XmlParser();
        xmlParser.xmlParse(v);
      },
      message: 'Provided XML is not valid.',
    },
  ],
});
</script>

<template>
  <div>
    <c-card title="Input">
      <c-input-text
        v-model:value="xslt"
        multiline
        placeholder="Put your xslt here..."
        rows="5"
        :validation="xsltValidation"
        mb-2
      />

      <c-input-text
        v-model:value="xmlInput"
        multiline
        placeholder="Put your xslt here..."
        rows="5"
        :validation="xmlInputValidation"
        mb-2
      />
    </c-card>

    <c-card title="Output">
      <TextareaCopyable :value="xmlOutput" language="xml" />
    </c-card>
  </div>
</template>
