<template>
  <q-dialog ref="dialog" position="bottom">
    <q-card style="width: 350px">
      {{ content }}
    </q-card>
  </q-dialog>
</template>

<script lang="ts">
import { Vue, Component, Ref, Prop } from 'vue-property-decorator';
import { QDialog } from 'quasar';

@Component<DefaultDialog>({
  name: 'DefaultDialog'
})
export default class DefaultDialog extends Vue {
  @Ref('dialog') public dialogRef!: QDialog;
  @Prop({ required: true }) public content!: string;

  public show() {
    this.dialogRef.show();
  }
}
</script>
